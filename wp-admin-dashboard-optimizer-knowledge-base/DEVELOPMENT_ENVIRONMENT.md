# Development Environment Setup

## Required VS Code Extensions

1. **PHP Development**
   - PHP Intelephense
   - PHP DocBlocker
   - PHP Namespace Resolver
   - PHP Constructor
   - PHP Getters & Setters

2. **WordPress Development**
   - WordPress Hooks IntelliSense
   - WordPress Snippets

3. **Testing & Quality**
   - PHP Test Explorer
   - Test Explorer UI
   - SonarLint
   - Error Lens

4. **API Development**
   - REST Client
   - Path Intellisense

5. **UI/UX Development**
   - Live Server
   - CSS Peek
   - IntelliSense for CSS
   - JavaScript (ES6) Code Snippets

6. **Version Control**
   - GitLens

7. **Security**
   - Security IntelliSense
   - PHP Security Suite

## VS Code Settings

Important settings have been configured for:
1. PHP development and WordPress standards
2. Security checks and code quality
3. Test configuration for TDD workflow
4. API testing tools
5. Database handling
6. Custom templates for security-focused code
7. UI/UX development tools

The complete settings.json configuration is available in:
`/Users/josephjauregui/Library/Application Support/Code/User/settings.json`

Key configurations include:
```json
{
  // PHP Sniffer & Security settings
  "phpsab.standard": "WordPress-Extra,WordPress-Core,WordPress-Docs,PHPCompatibilityWP,VariableAnalysis",
  
  // Test Explorer configuration
  "testExplorer.codeLens": true,
  "phptest.args": [
    "--colors=always",
    "--testdox",
    "--stop-on-failure"
  ],

  // Security IntelliSense rules
  "securityintellisense.rules": {
    "api-token-handling": "error",
    "secure-communication": "error",
    "input-validation": "error",
    "sensitive-data": "error",
    "encryption-required": "error",
    "secure-storage": "error"
  },

  // UI/UX Development settings
  "liveServer.settings.donotShowInfoMsg": true,
  "css.validate": true,
  "javascript.validate.enable": true,
  "javascript.suggestionActions.enabled": true
}
```

## Important Paths

1. **WordPress Installation**
   ```
   /Users/josephjauregui/Local Sites/cashformygiftcards/app/public
   ```

2. **Plugin Directory**
   ```
   /Users/josephjauregui/Local Sites/cashformygiftcards/app/public/wp-content/plugins/wp-admin-dashboard-optimizer-plugin
   ```

3. **Test Directories**
   ```
   /tests/api          # API Tests (Plaid & Authorize.net)
   /tests/integration  # WordPress Integration Tests
   /tests/unit        # Core Unit Tests
   ```

4. **UI/UX Assets**
   ```
   /assets/js          # JavaScript libraries and custom scripts
   /assets/css         # CSS styles and animations
   /assets/images      # Images and icons
   ```

## Overview
Complete development environment configuration for the WP Admin Dashboard Optimizer Plugin, focusing on secure financial transactions, API integrations, UI/UX enhancements, and WordPress best practices.

## UI/UX Development Setup

### Required NPM Packages
```bash
# Install UI/UX dependencies
npm install spin.js@4.1.0        # Loading animations
npm install nprogress@0.2.0      # Progress bars
npm install canvas-confetti@1.6.0 # Success celebrations
npm install toastify-js@1.11.2   # Notifications

# Development tools
npm install --save-dev webpack    # Asset bundling
npm install --save-dev sass      # CSS preprocessing
npm install --save-dev terser    # JavaScript minification
```

### Asset Build Configuration
```json
{
  "scripts": {
    "build": "webpack --mode production",
    "dev": "webpack --mode development --watch",
    "css": "sass src/scss:assets/css --style compressed",
    "watch:css": "sass src/scss:assets/css --watch"
  }
}
```

### UI/UX Development Workflow
1. Start development server: `npm run dev`
2. Watch CSS changes: `npm run watch:css`
3. Build for production: `npm run build`

[Rest of the original content remains unchanged...]

## Additional Notes
- PHP Version: 7.4+ required
- WordPress Version: 6.0+ required
- Node.js Version: 14.0+ required
- Memory Limit: Increased for development tools
- Custom autoloader configured for PSR-4
- Security-first approach for all features
- TDD workflow enforced through testing setup
- UI/UX enhancements follow WordPress standards

## VS Code Settings Deep Dive

[Previous VS Code settings sections remain unchanged...]

### UI/UX Development Settings
```json
"liveSassCompile.settings.formats": [
    {
        "format": "compressed",
        "extensionName": ".min.css",
        "savePath": "/assets/css"
    }
],
"javascript.format.enable": true,
"javascript.format.semicolons": "insert",
"javascript.preferences.quoteStyle": "single",
"css.validate": true,
"scss.validate": true
```

[Rest of the original content remains unchanged...]
