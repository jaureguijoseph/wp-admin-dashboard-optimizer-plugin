# AI Developer Quick Start Guide

## Project Context
This plugin appears to be a simple WordPress admin dashboard optimizer but actually handles secure gift card transactions. This security-through-obscurity approach is intentional and must be maintained throughout development.

## Key Documentation Files

1. **Core Understanding**
   - `HANDOFF_GUIDE.md` - Start here for overall architecture
   - `DEVELOPMENT_ROADMAP.md` - Development phases and next steps
   - `BRICKS_INTEGRATION.md` - Frontend integration details

2. **Security & APIs**
   - `SECURITY_MEASURES.md` - Security implementation details
   - `API_INTEGRATION.md``WPGETAPI_DOCUMENTATION.md``WPGETAPI_ENDPOINT_GUIDE.md``AUTHORIZE.NET_ACCEPT.JS_DOCS.md``PLAID_REQUIREMENTS_FOR_RTP.md``PLAID_TRANSFER_AND_TRACK_DOCS.md` - Plaid and Authorize.net integration
   - `TESTING.md` - TDD workflow and security testing

3. **Configuration**
   - `phpcs.xml` - Coding standards and security rules
   - `DEVELOPMENT_ENVIRONMENT.md` - Environment setup

## Development Approach
- [`PLUGIN_REQUIREMENTS.md`](./PLUGIN_REQUIREMENTS.md) - Plugin Requirements
- [`CUSTOMERS_PATH_THROUGH_CUSTOM_PLUGIN.md`](./CUSTOMERS_PATH_THROUGH_CUSTOM_PLUGIN.md) - Customers Journey Through Process
- [`WS_FORMS_TO_BE_USED_ON_WEBSITE/README.md`](./WS_FORMS_TO_BE_USED_ON_WEBSITE/README.md) - Documentation for WS Forms used on the website.

### 1. Security Through Obscurity
- Plugin appears as admin optimization tool
- Financial functions hidden in "optimization" methods
- Error messages masked as performance metrics
- API calls appear as admin operations

### 2. Code Organization
```
wp-admin-dashboard-optimizer/
├── includes/               # Core functionality
│   ├── Core/              # Main plugin logic
│   ├── Security/          # Encryption & security
│   ├── Database/          # Custom tables
│   ├── API/              
│   │   ├── Plaid/        # Bank integration
│   │   └── Authorize/    # Payment processing
│   └── Admin/            # WP admin interface
└── [Documentation files]
```

### 3. Integration Points
- Bricks Builder for landing pages
- WS Form Pro for transaction forms
- WordPress admin interface
- Plaid and Authorize.net APIs

## Development Workflow

1. **Before Coding**
   ```bash
   # Check current code standards
   phpcs --standard=phpcs.xml [file]
   
   # Auto-fix standards
   phpcbf --standard=phpcs.xml [file]
   ```

2. **During Development**
   - Maintain obscurity in function/class names
   - Use optimization-related terminology
   - Keep sensitive code hidden in utility classes
   - Follow TDD workflow

3. **Code Review**
   - Verify security measures
   - Check for exposed functionality
   - Ensure error messages maintain obscurity
   - Test API integrations

## Common Tasks

### 1. Adding New Features
```php
// Example: Adding transaction processing
class Dashboard_Optimizer {
    // Actually handles gift card transactions
    public function optimize_admin_performance($data) {
        // Process transaction here
        return $this->process_gift_card($data);
    }
}
```

### 2. Error Handling
```php
// Example: Generic error messages
$errors = [
    'transaction_failed' => 'Dashboard optimization failed',
    'invalid_data' => 'Invalid optimization parameters',
    'api_error' => 'Optimization service unavailable'
];
```

### 3. API Integration
```php
// Example: Plaid API call
public function check_optimization_metrics() {
    // Actually links bank account
    return $this->plaid_client->link_bank_account();
}
```

## Important Notes

1. **Security Priority**
   - Never expose true functionality in code or comments
   - Keep all financial data encrypted
   - Use generic error messages
   - Maintain admin optimization appearance

2. **Documentation**
   - Use optimization terminology in comments
   - Keep sensitive details secure
   - Document security measures carefully

3. **Testing**
   - Test all features thoroughly
   - Verify security measures
   - Check for exposed functionality
   - Validate API integrations

## Next Steps

1. Review all documentation files in order:
   - HANDOFF_GUIDE.md
   - DEVELOPMENT_ROADMAP.md
   - BRICKS_INTEGRATION.md
   - SECURITY_MEASURES.md
   - API_INTEGRATION.md
   - TESTING.md

2. Set up development environment:
   - Install required VS Code extensions
   - Configure PHP_CodeSniffer
   - Set up testing framework

3. Start development:
   - Follow TDD workflow
   - Maintain security through obscurity
   - Keep documentation updated

Remember: The success of this project relies on maintaining its appearance as a simple admin optimization tool while securely handling financial transactions.
