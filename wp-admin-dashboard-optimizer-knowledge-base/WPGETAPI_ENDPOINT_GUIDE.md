# WPGetAPI Pro - Endpoint Creation & Security Guide

[Previous content remains unchanged until Security Implementation section...]

## Security Implementation

### Authentication Methods
1. **API Keys**
   - Add to headers or query string
   - Secure storage in wp-config.php
   - Different keys for different environments
   - Can be added in two ways:
     * Query string: `?api_key=your_key`
     * Headers: `X-API-Key: your_key`

2. **Bearer Tokens**
   - Format: `Authorization: Bearer <token>`
   - Add in Headers section
   - Support for dynamic tokens
   - Commonly used with OAuth 2.0
   - Can be refreshed automatically

3. **Basic HTTP Authentication**
   - Username/password combination
   - Base64 encoded
   - Added to Authorization header
   - Format: `Authorization: Basic <base64_encoded_credentials>`
   - Credentials format: `username:password`

4. **OAuth 2.0**
   - Full OAuth flow support
   - Token management
   - Refresh token handling
   - Multiple grant types:
     * Authorization Code
     * Client Credentials
     * Password Grant
     * Refresh Token

### Advanced Security Features

1. **Request Signing**
   ```php
   // Example: Sign API requests
   function sign_api_request($args, $endpoint) {
       $timestamp = time();
       $nonce = wp_generate_password(12, false);
       
       $signature_data = array(
           'endpoint' => $endpoint['endpoint_id'],
           'timestamp' => $timestamp,
           'nonce' => $nonce
       );
       
       $signature = hash_hmac(
           'sha256',
           json_encode($signature_data),
           get_api_secret()
       );
       
       $args['headers']['X-Signature'] = $signature;
       $args['headers']['X-Timestamp'] = $timestamp;
       $args['headers']['X-Nonce'] = $nonce;
       
       return $args;
   }
   add_filter('wpgetapi_endpoint_args', 'sign_api_request', 10, 2);
   ```

2. **IP Whitelisting**
   ```php
   // Example: IP whitelist validation
   function validate_ip_access($args, $endpoint) {
       $allowed_ips = array(
           '192.168.1.1',
           '10.0.0.1'
       );
       
       $client_ip = $_SERVER['REMOTE_ADDR'];
       
       if (!in_array($client_ip, $allowed_ips)) {
           return new WP_Error(
               'ip_restricted',
               'Access denied from this IP address'
           );
       }
       
       return $args;
   }
   add_filter('wpgetapi_pre_request', 'validate_ip_access', 10, 2);
   ```

3. **Enhanced Rate Limiting**
   ```php
   // Example: Advanced rate limiting
   function advanced_rate_limiting($args, $endpoint) {
       $limits = array(
           'per_minute' => 60,
           'per_hour' => 1000,
           'per_day' => 10000
       );
       
       $user_id = get_current_user_id();
       
       // Check minute limit
       $minute_key = "rate_limit_{$user_id}_minute";
       $minute_count = get_transient($minute_key) ?: 0;
       
       if ($minute_count >= $limits['per_minute']) {
           return new WP_Error(
               'rate_limit_exceeded',
               'Minute rate limit exceeded'
           );
       }
       
       // Increment counters
       set_transient($minute_key, $minute_count + 1, 60);
       
       // Similar checks for hour and day...
       
       return $args;
   }
   add_filter('wpgetapi_pre_request', 'advanced_rate_limiting', 10, 2);
   ```

4. **Request Validation**
   ```php
   // Example: Comprehensive request validation
   function validate_request($args, $endpoint) {
       // Validate required headers
       if (!isset($args['headers']['Authorization'])) {
           return new WP_Error('missing_auth', 'Missing authorization');
       }
       
       // Validate token format
       if (isset($args['headers']['Authorization'])) {
           if (!preg_match('/^Bearer\s+[\w\-\.]+$/', $args['headers']['Authorization'])) {
               return new WP_Error('invalid_token', 'Invalid token format');
           }
       }
       
       // Validate content type
       if ($endpoint['method'] === 'POST') {
           if (!isset($args['headers']['Content-Type'])) {
               return new WP_Error('missing_content_type', 'Missing Content-Type header');
           }
       }
       
       // Validate payload
       if (isset($args['body'])) {
           if (!validate_payload($args['body'])) {
               return new WP_Error('invalid_payload', 'Invalid request payload');
           }
       }
       
       return $args;
   }
   add_filter('wpgetapi_pre_request', 'validate_request', 10, 2);
   ```

[Rest of previous content remains unchanged...]
