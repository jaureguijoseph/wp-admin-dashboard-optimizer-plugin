# Customer Path Through Gift Card Liquidation

## 🎯 Overview
This document provides a comprehensive end-to-end workflow combining backend architecture, error handling, UX/UI enhancements, third-party dependencies, and security measures for a seamless, robust, and scalable user experience.

## 📚 Phase 1: User Authentication and Initial Transaction Setup

### 1.1 User Authentication & Login

#### Process Overview
- Users securely log into the WordPress site via SSL/TLS encryption
- Authentication leverages WordPress's native user management system

#### Technical Details
- Add reCAPTCHA to prevent brute-force attacks
- Secure session management using HTTP-only cookies

#### Role-Based Access Control (RBAC)
- Default user role: Subscriber

#### 🔒 Security Notes
- Passwords are hashed using bcrypt
- Implement account lockout after failed login attempts

#### 🖥️ UI/UX Enhancements
- Loading Spinner (Spin.js): Displays while authentication is processed
- Toast Notification (Toastify.js):
  - ✅ "Login Successful! Redirecting to your dashboard."
  - ❌ "Invalid credentials. Please try again."

#### 📦 Dependencies Used
- spin.js (v4.1.0): For loading spinner during login
- toastify-js (v1.11.2): For toast notifications after login

### 1.2 Dashboard Access & Transaction Initiation

#### Process Overview
- Upon successful login, users access their Dynamic Dashboard (via JetEngine)
- A "Sell My Gift Card" button (via WSForm Pro) initiates the process

#### Technical Details
- WSForm contains dynamically pre-filled hidden fields:
  - First Name
  - Last Name
  - Date of Birth
  - Email
  - WordPress User ID
  - Invoice Number
  - User IP Address

#### Transaction Validation
- Daily Limit: $500 (past 24 hours)
- Weekly Limit: $1,800 (rolling 7 days)
- Monthly Limit: $3,600 (since 1st day of current month)
- Yearly Limit: $8,800 (since January 1st of current year)

#### Error Handling
- ❌ If limits are exceeded:
  - Toast Notification: "Transaction limit exceeded. Next available transaction in [X] days for $[Y]"
- Role Change:
  - Temporary role: PLAID (expires after 30 minutes)

#### 🖥️ UI/UX Enhancements
- Loading Spinner (Spin.js): Displays during transaction validation
- Progress Bar (NProgress.js): Shows progress during validation
- Toast Notification (Toastify.js): Friendly error messages if validation fails

#### 📦 Dependencies Used
- spin.js (v4.1.0): Loading spinner for validation
- nprogress.js (v0.2.0): Progress bar for validation steps
- toastify-js (v1.11.2): Validation error messages

## 📚 Phase 2: Bank Account Linking with Plaid

### 2.1 Plaid Modal Trigger & Account Linking

#### Process Overview
- The Plaid Modal securely facilitates bank account linking
- Only users with PLAID role can access this step

#### Technical Details
- Plaid generates a unique access token for the linked account
- Token is encrypted using AES-256 before storage
- Role Change:
  - User role changes to TRANSACTION upon success

#### 🖥️ UI/UX Enhancements
- Loading Spinner (Spin.js): While modal loads
- Toast Notification (Toastify.js):
  - ✅ "Bank account successfully linked"
  - ❌ "Failed to link your bank account. Please retry"

#### 📦 Dependencies Used
- spin.js (v4.1.0): Loading spinner
- toastify-js (v1.11.2): Success/error notifications

### 2.2 RTP Eligibility Check

#### Process Overview
- Use Plaid's /transfer/capabilities/get to check RTP eligibility
- Verify bank supports real-time payments

#### Error Handling
- ❌ If RTP is not supported:
  - Toast Notification: "Your bank does not support RTP payments. Please try a different account"
  - Retry Option: Button to relink another account

#### 🖥️ UI/UX Enhancements
- Toast Notification (Toastify.js): Clear messaging about RTP failure
- Progress Bar (NProgress.js): Real-time eligibility check

#### 📦 Dependencies Used
- nprogress.js (v0.2.0): RTP eligibility check
- toastify-js (v1.11.2): Error messages

### 2.3 Identity Verification with Plaid

#### Process Overview
- Validate user identity via Plaid Identity API
- Cross-reference with WordPress user data

#### Data Verification
- First Name
- Last Name
- Date of Birth
- User ID

#### Error Handling
- ❌ If verification fails:
  - Toast Notification: "Identity verification failed. Please retry"
  - Option to update profile or try different account
- Role Update: User proceeds to Phase 3 on success

#### 🖥️ UI/UX Enhancements
- Toast Notification (Toastify.js): Verification results
- Loading Spinner (Spin.js): During verification checks

#### 📦 Dependencies Used
- spin.js (v4.1.0): Loading spinner
- toastify-js (v1.11.2): Verification feedback

## 📚 Phase 3: User Inputs Transaction Amount

### Process Overview
- Only users with TRANSACTION role can access this step
- User enters gift card value through WSForm Pro

### Technical Details
- WSForm validates input ranges ($20 to $500)
- Additional validation ensures numeric values only

### 🖥️ UI/UX Enhancements
- Real-time validation feedback
- Dynamic fee calculation display
- Progress indicator showing step in overall process

### 📦 Dependencies Used
- WSForm Pro: Form handling and validation
- toastify-js (v1.11.2): Validation messages

## 📚 Phase 4: Payment Processing with Authorize.NET

### Process Overview
- Authorize.NET modal launches for gift card details
- WSForm Pro authorize.net extension handles payment processing

### Technical Details
- All payment data handled by Authorize.NET
- No sensitive card data touches our servers
- Webhook notifications for transaction status

### 🖥️ UI/UX Enhancements
- Loading animations during processing
- Success/failure animations
- Clear status messages

### 📦 Dependencies Used
- Canvas Confetti (v1.6.0): Success celebrations
- spin.js (v4.1.0): Loading animations

## 📚 Phase 5: Payout to User via Plaid RTP or FedNow

### Process Overview
- Automatic role change to "Pay Out" after successful payment
- Plaid token decryption and verification
- Real-time payment initiation

### Technical Details
- Fee calculation (15% + $1.50)
- Secure token handling
- Transaction logging

### 🖥️ UI/UX Enhancements
- Progress tracking for payout
- Success animation with confetti
- Clear confirmation messages

### 📦 Dependencies Used
- Canvas Confetti (v1.6.0): Success celebration
- nprogress.js (v0.2.0): Payout progress tracking

## 📚 Phase 6: User Notification and Finalization

### Process Overview
- Email/SMS notification of successful payout
- Transaction history update
- Token cleanup

### Technical Details
- Secure deletion of Plaid token
- Transaction logging
- Audit trail creation

### 🖥️ UI/UX Enhancements
- Success page with transaction details
- Animated confirmation checkmark
- Option to start new transaction

### 📦 Dependencies Used
- Canvas Confetti (v1.6.0): Success celebration
- toastify-js (v1.11.2): Confirmation messages

## 📦 Dependencies Overview

### UI/UX Libraries
- Spin.js (v4.1.0): Loading animations
- NProgress.js (v0.2.0): Progress bars
- Canvas Confetti (v1.6.0): Success celebrations
- Toastify.js (v1.11.2): Notifications

### WordPress Plugins
- WS Form Pro: Form handling and Authorize.NET integration
- JetEngine: Dynamic user profiles
- WPGetAPI Pro: API integration management
- Bricks Builder: Frontend design

### Security
- reCAPTCHA: Prevent brute force attacks
- bcrypt: Password hashing
- AES-256: Data encryption

## 🔒 Security Measures

### Data Protection
- All sensitive data encrypted with AES-256
- Secure token management
- PCI compliance maintained

### Access Control
- Role-based access
- Session management
- Timeout handling

### Error Handling
- Comprehensive error logging
- User-friendly error messages
- Automatic retry mechanisms

## 📊 Performance Optimization

### Caching
- API response caching
- Transaction history optimization
- User data caching

### Database
- Indexed custom tables
- Optimized queries
- Regular cleanup of expired data

Remember: Every step maintains the appearance of a simple admin optimization tool while securely handling financial transactions.
