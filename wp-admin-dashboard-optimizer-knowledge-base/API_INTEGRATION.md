# API Integration Guide

## Overview
This guide details the secure integration of Plaid and Authorize.net APIs within the WP Admin Dashboard Optimizer Plugin. It covers authentication, token management, webhook handling, and security best practices.

## Table of Contents
- [Plaid Integration](#plaid-integration)
- [Authorize.net Integration](#authorizenet-integration)
- [Security Measures](#security-measures)
- [Error Handling](#error-handling)
- [Testing](#testing)

## Plaid Integration

### Authentication Flow
```php
/**
 * 1. Initialize Plaid Link
 * 2. Exchange public token
 * 3. Store access token securely
 */
class PlaidAuthentication {
    public function initializePlaidLink() {
        $config = [
            'client_name' => 'Cash For My Gift Cards',
            'products' => ['auth'],
            'country_codes' => ['US'],
            'language' => 'en'
        ];
        return $this->plaid_client->createLinkToken($config);
    }

    public function exchangeToken($public_token) {
        try {
            $exchange_response = $this->plaid_client->exchangePublicToken($public_token);
            $access_token = $exchange_response['access_token'];
            
            // Encrypt before storage
            return $this->token_manager->securelyStoreToken($access_token);
        } catch (Exception $e) {
            $this->error_handler->logError('Plaid token exchange failed', [
                'error' => $e->getMessage(),
                'public_token' => '[REDACTED]'
            ]);
            throw new PlaidIntegrationException($e->getMessage());
        }
    }
}
```

### Webhook Implementation
```php
/**
 * Handle Plaid webhooks securely
 */
class PlaidWebhookHandler {
    public function handleWebhook(WP_REST_Request $request) {
        // Verify webhook signature
        if (!$this->verifyWebhookSignature($request)) {
            wp_send_json_error('Invalid signature', 401);
            return;
        }

        $payload = $request->get_json_params();
        
        switch ($payload['webhook_type']) {
            case 'TRANSFER':
                return $this->handleTransferWebhook($payload);
            case 'ITEM':
                return $this->handleItemWebhook($payload);
            default:
                wp_send_json_error('Unsupported webhook type', 400);
        }
    }

    private function verifyWebhookSignature($request) {
        $signature = $request->get_header('Plaid-Verification');
        $body = $request->get_body();
        
        return hash_equals(
            hash_hmac('sha256', $body, CFMGC_PLAID_WEBHOOK_SECRET),
            $signature
        );
    }
}
```

## Authorize.net Integration

### Transaction Processing
```php
/**
 * Handle secure payment processing
 */
class AuthorizeNetProcessor {
    public function processPayment($amount, $payment_data) {
        // Sanitize and validate input
        $sanitized_amount = $this->sanitize_amount($amount);
        if (!$this->validate_payment_data($payment_data)) {
            throw new ValidationException('Invalid payment data');
        }

        try {
            // Create and execute transaction
            $transaction = $this->createTransaction($sanitized_amount, $payment_data);
            $response = $this->executeTransaction($transaction);
            
            // Log transaction securely
            $this->logTransaction($response, $sanitized_amount);
            
            return $this->handleResponse($response);
        } catch (Exception $e) {
            $this->error_handler->logError('Payment processing failed', [
                'amount' => $sanitized_amount,
                'error' => $e->getMessage()
            ]);
            throw new PaymentProcessingException($e->getMessage());
        }
    }

    private function createTransaction($amount, $payment_data) {
        return [
            'transactionRequest' => [
                'transactionType' => 'authCaptureTransaction',
                'amount' => $amount,
                'payment' => $this->sanitize_payment_data($payment_data),
                'order' => [
                    'invoiceNumber' => $this->generate_invoice_number(),
                    'description' => 'Gift Card Transaction'
                ]
            ]
        ];
    }
}
```

### Response Handling
```php
/**
 * Handle Authorize.net API responses
 */
class AuthorizeNetResponseHandler {
    public function handleResponse($response) {
        if ($response->isSuccessful()) {
            return $this->processSuccessfulResponse($response);
        } else {
            return $this->processErrorResponse($response);
        }
    }

    private function processSuccessfulResponse($response) {
        // Encrypt sensitive data before storage
        $transaction_id = $response->getTransactionID();
        $encrypted_data = $this->encryption->encrypt(json_encode([
            'transaction_id' => $transaction_id,
            'auth_code' => $response->getAuthCode(),
            'response_code' => $response->getResponseCode()
        ]));

        // Store in database
        $this->db->storeTransactionData($encrypted_data);

        return [
            'success' => true,
            'transaction_id' => $transaction_id
        ];
    }
}
```

## Security Measures

### Token Management
```php
/**
 * Secure token management
 */
class TokenManager {
    private $encryption;
    private $db;

    public function __construct(Encryption $encryption, Database $db) {
        $this->encryption = $encryption;
        $this->db = $db;
    }

    public function securelyStoreToken($token, $type = 'plaid') {
        // Generate unique identifier
        $token_id = wp_generate_uuid4();
        
        // Encrypt token
        $encrypted_token = $this->encryption->encrypt($token);
        
        // Store in database
        $this->db->storeToken($token_id, $encrypted_token, $type);
        
        return $token_id;
    }

    public function retrieveToken($token_id) {
        $encrypted_token = $this->db->getToken($token_id);
        if (!$encrypted_token) {
            throw new TokenNotFoundException('Token not found');
        }
        
        return $this->encryption->decrypt($encrypted_token);
    }
}
```

### Data Encryption
```php
/**
 * Encryption service using libsodium
 */
class Encryption {
    public function encrypt($data) {
        $nonce = random_bytes(SODIUM_CRYPTO_SECRETBOX_NONCEBYTES);
        $encrypted = sodium_crypto_secretbox(
            $data,
            $nonce,
            $this->getEncryptionKey()
        );
        
        return base64_encode($nonce . $encrypted);
    }

    public function decrypt($encrypted_data) {
        $decoded = base64_decode($encrypted_data);
        $nonce = mb_substr($decoded, 0, SODIUM_CRYPTO_SECRETBOX_NONCEBYTES, '8bit');
        $ciphertext = mb_substr($decoded, SODIUM_CRYPTO_SECRETBOX_NONCEBYTES, null, '8bit');
        
        return sodium_crypto_secretbox_open(
            $ciphertext,
            $nonce,
            $this->getEncryptionKey()
        );
    }
}
```

## Error Handling

### API Error Handler
```php
/**
 * Centralized API error handling
 */
class APIErrorHandler {
    public function handleError($error, $context = []) {
        // Log error securely
        $this->logError($error, $context);
        
        // Determine error type and response
        if ($error instanceof ValidationException) {
            return $this->handleValidationError($error);
        } elseif ($error instanceof APIException) {
            return $this->handleAPIError($error);
        }
        
        // Default error response
        return $this->createErrorResponse($error);
    }

    private function logError($error, $context) {
        error_log(wp_json_encode([
            'error' => $this->sanitizeErrorMessage($error),
            'context' => $this->sanitizeContext($context),
            'timestamp' => current_time('mysql'),
            'request_id' => wp_generate_uuid4()
        ]));
    }
}
```

## Testing

### API Integration Tests
```php
/**
 * Test API integrations
 */
class APIIntegrationTest extends WP_UnitTestCase {
    public function testPlaidTokenExchange() {
        $public_token = 'public-sandbox-token';
        $plaid_auth = new PlaidAuthentication();
        
        $result = $plaid_auth->exchangeToken($public_token);
        
        $this->assertNotEmpty($result);
        $this->assertTrue($this->token_manager->validateToken($result));
    }

    public function testAuthorizeNetTransaction() {
        $amount = 100.00;
        $payment_data = $this->getMockPaymentData();
        
        $processor = new AuthorizeNetProcessor();
        $result = $processor->processPayment($amount, $payment_data);
        
        $this->assertTrue($result['success']);
        $this->assertNotEmpty($result['transaction_id']);
    }
}
```

## Best Practices

1. **Token Management**
   - Never store unencrypted tokens
   - Implement token rotation
   - Use secure storage methods

2. **Error Handling**
   - Log errors securely
   - Sanitize error messages
   - Implement proper fallbacks

3. **Security**
   - Validate all inputs
   - Encrypt sensitive data
   - Use prepared statements
   - Implement rate limiting

4. **Testing**
   - Use sandbox environments
   - Test error scenarios
   - Validate security measures

## Webhook URLs

### Development
```
https://cashformygiftcards.local/wp-json/cfmgc/v1/webhooks/plaid
https://cashformygiftcards.local/wp-json/cfmgc/v1/webhooks/authorize
```

### Production
```
https://cashformygiftcards.com/wp-json/cfmgc/v1/webhooks/plaid
https://cashformygiftcards.com/wp-json/cfmgc/v1/webhooks/authorize
```

## Environment Variables

Required environment variables for API integration:
```bash
# Plaid API
CFMGC_PLAID_CLIENT_ID=your_client_id
CFMGC_PLAID_SECRET=your_secret
CFMGC_PLAID_ENV=sandbox|development|production
CFMGC_PLAID_WEBHOOK_SECRET=your_webhook_secret

# Authorize.net API
CFMGC_AUTHNET_API_LOGIN_ID=your_login_id
CFMGC_AUTHNET_TRANSACTION_KEY=your_transaction_key
CFMGC_AUTHNET_ENV=sandbox|production
CFMGC_AUTHNET_SIGNATURE_KEY=your_signature_key

# Encryption
CFMGC_ENCRYPTION_KEY=your_encryption_key
```

## Support

For API integration issues:
1. Check the error logs
2. Verify environment variables
3. Test in sandbox environment
4. Contact API provider support

## Additional Resources

- [Plaid API Documentation](https://plaid.com/docs/)
- [Authorize.net Developer Guides](https://developer.authorize.net/)
- [WordPress REST API Handbook](https://developer.wordpress.org/rest-api/)
