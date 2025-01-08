# WP Admin Dashboard Optimizer Plugin - Development Instructions

## Core Principles

### Technical Architecture
- Backend workflows for secure gift card transactions
- Plaid and Authorize.NET API integrations
- Custom database tables with encryption
- WordPress role-based access control
- Security through obscurity approach

### Error Handling
- Comprehensive error logging with 90-day retention
- Clear resolution paths for API failures
- Transaction retry mechanisms
- User-friendly error messages
- Admin notification system

### UX/UI Enhancements
- Loading spinners (spin.js@4.1.0)
- Progress bars (nprogress@0.2.0)
- Success celebrations (canvas-confetti@1.6.0)
- Toast notifications (toastify-js@1.11.2)
- Role-based UI states

### Dependencies
- WordPress 6.0+
- PHP 7.4+
- Node.js 14.0+
- WS Form Pro with Authorize.NET extension
- JetEngine for dynamic profiles
- WPGetAPI Pro for API management
- Bricks Builder for frontend

### Security
- AES-256 encryption for sensitive data
- Token-based authentication
- Role expiration (30-minute timeouts)
- Transaction limits enforcement
- Hidden username validation

### Integration Points
- Plaid for bank account linking
- Authorize.NET for payment processing
- WS Form Pro for transaction forms
- JetEngine for user profiles
- WordPress core for user management

## Development Standards

### 1. Test-Driven Development (PRIORITY 1)
- Write failing test first (Red)
- Write minimal code to pass (Green)
- Refactor while maintaining tests (Refactor)
- Comprehensive test coverage required
- Integration tests for all APIs

### 2. Security Through Obscurity
- Plugin appears as admin optimization tool
- Generic function and file names
- Masked error messages
- Hidden transaction processing
- Encrypted sensitive data

### 3. WordPress Integration
- Custom user roles (PLAID, TRANSACTION, PAYOUT)
- Role-based access control
- Custom database tables
- WordPress coding standards
- Proper hook usage

### 4. API Security
- Token encryption
- Webhook validation
- Rate limiting
- Error recovery
- Secure token storage

## Implementation Guide

### Phase 1: User Authentication
```php
// Example: Role Assignment
function assign_plaid_role($user_id) {
    $expiration = time() + (30 * MINUTE_IN_SECONDS);
    wp_set_current_user($user_id);
    add_user_meta($user_id, 'role_expiration', $expiration);
    $user = new WP_User($user_id);
    $user->set_role('plaid_user');
}
```

### Phase 2: Transaction Processing
```php
// Example: Transaction Limit Check
function check_transaction_limits($user_id) {
    $daily_limit = 500;
    $weekly_limit = 1800;
    $monthly_limit = 3600;
    $yearly_limit = 8800;
    
    // Check limits and return available amount/time
    return calculate_next_available_transaction($user_id);
}
```

### Phase 3: Bank Account Linking
```php
// Example: Plaid Token Encryption
function encrypt_plaid_token($token) {
    $encryption_key = get_option('gciss_encryption_key');
    return openssl_encrypt($token, 'aes-256-cbc', $encryption_key, 0, GCISS_IV);
}
```

### Phase 4: Payment Processing
```php
// Example: Authorize.NET Integration
function process_payment($amount, $card_data) {
    $auth_client = new CFMGC\API\Authorize\Client();
    return $auth_client->createTransaction([
        'amount' => sanitize_amount($amount),
        'card' => encrypt_card_data($card_data)
    ]);
}
```

### Phase 5: Error Handling
```php
// Example: Error Logging
function log_error($error_code, $description, $user_id = null) {
    global $wpdb;
    $wpdb->insert(
        "{$wpdb->prefix}gciss_error_logs",
        [
            'error_code' => $error_code,
            'description' => $description,
            'user_id' => $user_id,
            'error_time' => current_time('mysql')
        ]
    );
}
```

## Database Schema

### Transaction Table
```sql
CREATE TABLE `{$wpdb->prefix}cfmgc_transactions` (
    `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `transaction_hash` char(64) NOT NULL,
    `encrypted_data` longtext NOT NULL,
    `created_at` datetime NOT NULL,
    PRIMARY KEY (`id`),
    KEY `transaction_hash` (`transaction_hash`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

### Error Logs Table
```sql
CREATE TABLE `{$wpdb->prefix}cfmgc_error_logs` (
    `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `error_code` varchar(50) NOT NULL,
    `error_description` text NOT NULL,
    `user_id` bigint(20) unsigned DEFAULT NULL,
    `phase` varchar(50),
    `date_logged` datetime NOT NULL,
    PRIMARY KEY (`id`),
    KEY `user_id` (`user_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

## UI/UX Implementation

### Loading States
```javascript
// Example: Transaction Processing Spinner
const spinner = new Spin.js({
    lines: 12,
    length: 7,
    width: 5,
    radius: 10,
    color: '#007bff'
}).spin(document.getElementById('loading-target'));
```

### Progress Indicators
```javascript
// Example: Bank Linking Progress
NProgress.configure({ 
    minimum: 0.1,
    showSpinner: false
});
NProgress.start();
// ... bank linking process
NProgress.done();
```

### Success Notifications
```javascript
// Example: Transaction Success
confetti({
    particleCount: 100,
    spread: 70,
    origin: { y: 0.6 }
});
```

### Error Notifications
```javascript
// Example: Error Toast
Toastify({
    text: "Transaction limit exceeded. Next available: $500 in 24 hours",
    duration: 5000,
    gravity: "top",
    position: "right",
    backgroundColor: "#dc3545"
}).showToast();
```

## Testing Requirements

### Unit Tests
```php
// Example: Transaction Validation Test
public function test_transaction_limits() {
    $validator = new TransactionValidator();
    $this->assertFalse($validator->checkLimits(600)); // Over daily limit
    $this->assertTrue($validator->checkLimits(400)); // Within limits
}
```

### Integration Tests
```php
// Example: Plaid API Test
public function test_plaid_token_exchange() {
    $plaid = new PlaidIntegration();
    $result = $plaid->exchangePublicToken('test_token');
    $this->assertNotEmpty($result->access_token);
}
```

## Security Measures

### Token Encryption
```php
// Example: Token Encryption
function encrypt_sensitive_data($data) {
    return sodium_crypto_secretbox(
        $data,
        NONCE,
        ENCRYPTION_KEY
    );
}
```

### Role Management
```php
// Example: Role Expiration Check
function check_role_expiration($user_id) {
    $expiration = get_user_meta($user_id, 'role_expiration', true);
    if (time() > $expiration) {
        revert_to_subscriber($user_id);
    }
}
```

## Performance Optimization

### Caching
```php
// Example: Transaction History Caching
function get_transaction_history($user_id) {
    $cache_key = "transaction_history_{$user_id}";
    $cached = wp_cache_get($cache_key);
    if (false === $cached) {
        $cached = fetch_transaction_history($user_id);
        wp_cache_set($cache_key, $cached, '', 300);
    }
    return $cached;
}
```

### Database Optimization
```php
// Example: Custom Table Indexes
function add_custom_indexes() {
    global $wpdb;
    $wpdb->query("ALTER TABLE {$wpdb->prefix}cfmgc_transactions ADD INDEX `user_transaction` (`user_id`, `created_at`)");
}
```


