# AI Handoff Guide

## Overview
This guide explains how the WP Admin Dashboard Optimizer Plugin (our secure gift card transaction handler) integrates into the larger WordPress ecosystem while maintaining security through obscurity. The plugin appears as a simple admin optimization tool but actually handles secure financial transactions.

## Complete WordPress Architecture

### 1. WordPress Core Structure
```
root
├── wp-admin/
├── wp-includes/
├── wp-content/
│   ├── themes/
│   │   └── bricks/           # Main theme for page building
│   ├── plugins/
│   │   ├── wp-admin-dashboard-optimizer/  # Our plugin (appears harmless)
│   │   └── other-plugins/    # WS Form Pro, etc.
│   └── uploads/
└── wp-config.php
```

### 2. Bricks Builder Integration
- Landing pages built with Bricks:
  - Visa Gift Card (/visa-gift-card/)
  - Mastercard Gift Card (/mastercard-gift-card/)
  - American Express Gift Card (/american-express-gift-card/)
  - Discover Gift Card (/discover-gift-card/)
- Global blocks for reusable components:
  - FAQ sections
  - Testimonials
  - CTA blocks

## Our Plugin Architecture

### 1. Plugin Root (appears as admin optimization tool)
```
wp-admin-dashboard-optimizer/
├── wp-admin-dashboard-optimizer.php  # Main plugin file
├── includes/
│   ├── Core/           # Core functionality
│   │   ├── Activator.php
│   │   ├── Deactivator.php
│   │   └── Loader.php
│   ├── Security/       # Encryption & security
│   │   ├── Encryption.php
│   │   ├── TokenManager.php
│   │   └── DataSanitizer.php
│   ├── Database/       # Custom tables
│   │   ├── Manager.php
│   │   └── Tables/
│   ├── API/           
│   │   ├── Plaid/     # Bank account linking
│   │   └── Authorize/ # Payment processing
│   └── Admin/         # WP admin interface
├── phpcs.xml          # Coding standards
- **Purpose**: Configuration file for PHP_CodeSniffer that enforces coding standards and security rules
- **Location**: Root of the project directory
- **Importance**: Critical for maintaining code quality and security
- **Key Features**:
  - WordPress Coding Standards integration
  - Security rules for financial data
  - Custom sniffs for secure coding practices
  - PHP 7.4+ compatibility checks
  - Forbidden functions for security
  - Strict type enforcement

### 2. Configuration & Documentation
- **README.md**: Project overview and quick start guide
- **DEVELOPMENT_ENVIRONMENT.md**: Complete development setup instructions
- **SECURITY_MEASURES.md**: Security implementation details
- **API_INTEGRATION.md**: Plaid and Authorize.net integration guide
- **TESTING.md**: TDD workflow and testing procedures
- **CHANGELOG.md**: Version tracking and changes

### 3. Integration Points
- **WS Form Pro**: Custom forms for gift card processing
- **Bricks Builder**: Landing pages and user interface
- **WordPress Core**: Admin interface and user management
- **Plaid API**: Bank account verification
- **Authorize.net**: Payment processing
- **wp-admin-dashboard-optimizer.php**
  - Entry point for the plugin
  - Intentionally named for security through obscurity
  - Handles autoloading and initialization
  - Sets up secure transaction processing

### 4. Database Tables (hidden behind wp_ prefix)
```sql
wp_cfmgc_transactions
wp_cfmgc_tokens
wp_cfmgc_logs
```

## Development & Testing Workflow

1. **Code Standards**
   - Use phpcs.xml for code validation:
   ```bash
   phpcs --standard=phpcs.xml [file]
   ```
   - Auto-fix standards:
   ```bash
   phpcbf --standard=phpcs.xml [file]
   ```

2. **Security Practices**
   - All financial data must be encrypted
   - Use prepared statements for SQL
   - Validate all inputs
   - Follow TDD workflow

3. **Testing Requirements**
   - Unit tests required for all new features
   - Integration tests for API functionality
   - Security tests for data handling

## Security Through Obscurity Strategy

1. **Plugin Appearance**
   - Named as admin optimization tool
   - Admin interface shows optimization metrics
   - Financial functions hidden in generic classes

2. **File Structure**
   - Generic file names (e.g., Optimizer.php, Enhancer.php)
   - Sensitive code buried in utility classes
   - Transaction logic hidden in "optimization" methods

3. **Database Tables**
   - Generic table names
   - Encrypted sensitive data
   - Table structure appears administrative

4. **Integration Points**
   - API calls masked as admin operations
   - Plaid integration hidden in optimization logic
   - Authorize.net calls appear as dashboard updates

1. **Security Through Obscurity**
   - The plugin appears as an admin optimization tool
   - Keep sensitive functionality hidden in generic-looking files
   - Use obfuscated class and method names

2. **API Integration**
   - Plaid for bank account linking
   - Authorize.net for payment processing
   - All API credentials must be encrypted

3. **Error Handling**
   - Log errors securely
   - Never expose sensitive data in errors
   - Use generic error messages in production

4. **Database Operations**
   - Always use WordPress prepared statements
   - Encrypt sensitive data before storage
   - Regular backup procedures

## Next Steps for AI Development

1. **Core Implementation**
   - Build "optimization" dashboard
   - Implement hidden transaction logic
   - Create secure API handlers

2. **Integration**
   - Connect with Bricks templates
   - Integrate WS Form Pro
   - Set up API connections

3. **Testing**
   - Unit tests for all components
   - Integration tests with APIs
   - Security penetration testing

4. **Documentation**
   - Maintain obscurity in docs
   - Use optimization terminology
   - Keep sensitive details secure

Remember: Every aspect of the plugin must maintain the appearance of a simple admin optimization tool while securely handling financial transactions.
