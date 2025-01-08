# WP Admin Dashboard Optimizer Knowledge Base

## ⚠️ PRIORITY 1: TEST-DRIVEN DEVELOPMENT (TDD) ⚠️
All development MUST follow Test-Driven Development principles. See [Testing Guide](TESTING.md) for the mandatory TDD workflow.

## Quick Start
1. [Testing Guide](TESTING.md) - **Start Here: TDD Workflow (REQUIRED)**
2. [Development Roadmap](DEVELOPMENT_ROADMAP.md) - Architecture & Development Plan
3. [Plugin Requirements](PLUGIN_REQUIREMENTS.md) - Plugin Requirements
4. [Customer Journey](CUSTOMER_PATH_THROUGH_CUSTOM_PLUGIN.md) - Customer Process Flow
5. [Handoff Guide](HANDOFF_GUIDE.md) - Architecture Overview

## Core Documentation
1. [Testing Guide](TESTING.md) - TDD Workflow (Priority 1)
2. [Plugin Requirements](PLUGIN_REQUIREMENTS.md) - Lists Most Requirements Of The Custom Plugin[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[[''[[[[[[[[[[[[[[[[['[[]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]
3. [Development Environment](DEVELOPMENT_ENVIRONMENT.md) - Setup Guide
4. [Security Measures](SECURITY_MEASURES.md) - Security Implementation
5. [API Integration][def] - Plaid & Authorize.net
6. [Bricks Integration](BRICKS_INTEGRATION.md) - Frontend Integration

## Configuration
1. [phpcs.xml](phpcs.xml) - Coding Standards
2. [Changelog](CHANGELOG.md) - Version History
3. [README](README.md) - Project Overview

## Development Process

### 1. Testing Setup (PRIORITY 1)
1. Review [Testing Guide](TESTING.md) for mandatory TDD workflow
2. Set up test environment
3. Write initial test suites
4. Configure CI/CD for tests

### 2. Initial Setup
1. Set up environment using [Development Environment](DEVELOPMENT_ENVIRONMENT.md)
2. Configure coding standards with [phpcs.xml](phpcs.xml)
3. Review security through obscurity approach

### 3. Security Implementation
1. Study [Security Measures](SECURITY_MEASURES.md)
2. Review security through obscurity approach
3. Implement encryption and data protection

### 4. API Integration
1. Study Authorize.net [Authorize.net integration](AUTHORIZE.NET_ACCEPT.JS_DOCS.md)
2. Study WP Get API Pro Docs [WPGet API Pro](WPGETAPI_DOCUMENTATION.md)
3. Study WP Get API Pro Endpoint Docs [WPGet API Pro Custom Endpoints](WPGETAPI_ENDPOINT_GUIDE.md)
4. Prepare for Plaid integration [Review Plaid Requirements & Plaid docs](PLAID_REQUIREMENTS_FOR_RTP.md)
5. Set Up Plaid Integration [Review Plaid Docs](PLAID_TRANSFER_AND_TRACK_DOCS.md)
6. Configure Authorize.net (via WS-Form-authorize.net-extension-plugin)
7. Implement webhooks

### 5. Frontend Development
1. Review [Bricks Integration](BRICKS_INTEGRATION.md)
2. Create user dashboard
3. Create error table & past transactions table in user dashboard 
4. Review all ws forms in folder named [Review ws forms](WS_FORMS_TO_BE_USED_ON_WEBSITE)
5. Ensure there is a way for admin to insert hidden WS Form Disguised as a button

## File Architecture
Located in: `/Users/josephjauregui/Local Sites/cashformygiftcards/app/public/wp-content/plugins/wp-admin-dashboard-optimizer-plugin`

```
wp-admin-dashboard-optimizer-plugin/
├── wp-admin-dashboard-optimizer.php   # Main plugin file
├── tests/                            # TDD test suites
│   ├── bootstrap.php                 # Test initialization
│   ├── unit/                         # Unit tests (TDD)
│   │   ├── Core/                     # Core functionality tests
│   │   ├── Security/                 # Security feature tests
│   │   ├── Database/                 # Database operation tests
│   │   └── API/                      # API integration tests
│   ├── integration/                  # Integration tests
│   └── security/                     # Security-specific tests
├── includes/                         # Plugin source code
│   ├── Core/                         # Core functionality
│   │   ├── Activator.php            # Plugin activation
│   │   ├── Deactivator.php          # Plugin deactivation
│   │   └── Loader.php               # Class autoloader
│   ├── Security/                     # Security measures
│   │   ├── Encryption.php           # Data encryption
│   │   ├── TokenManager.php         # Token handling
│   │   └── DataSanitizer.php        # Input sanitization
│   ├── Database/                     # Database operations
│   │   ├── Manager.php              # Database management
│   │   └── Tables/                  # Custom table definitions
│   ├── API/                         # API integrations
│   │   ├── Plaid/                   # Plaid API handlers
│   │   └── Authorize/               # Authorize.net handlers
│   └── Admin/                       # WordPress admin interface
└── wp-admin-dashboard-optimizer-knowledge-base/  # Documentation
    ├── TESTING.md                   # TDD workflow (Priority 1)
    ├── API_INTEGRATION.md           # API integration docs
    ├── SECURITY_MEASURES.md         # Security implementation
    └── [Other documentation files]
```

## Important Notes

### Test-Driven Development (PRIORITY 1)
- Write tests BEFORE implementing features
- Follow Red-Green-Refactor cycle strictly
- Maintain comprehensive test coverage
- Document all test cases thoroughly

### Security Through Obscurity
- Plugin appears as admin optimization tool
- Financial functions hidden in optimization methods
- Error messages masked as performance metrics
- API calls appear as admin operations

### Code Standards
- Follow WordPress coding standards
- Use phpcs.xml configuration
- Maintain security best practices
- Document all changes

### Documentation
- Keep documentation updated
- Use optimization terminology
- Maintain security through obscurity
- Document all integrations

[def]: API_INTEGRATION.md
