To enable visitors to your website to link their bank accounts, verify
their identity, and receive instant payments, you will need to integrate
several Plaid products. Here\'s a detailed guide on the necessary
products and their functionalities:

1\. Plaid Link

Plaid Link is the client-side component that allows your users to
securely connect their bank accounts to your application. It is the
starting point for creating an Item, which represents a single login at
a financial institution.

Key Steps:

-   **Create a Link Token**: This token is used to initialize Plaid
    Link.

-   **Initialize Plaid Link**: Use the Link token to launch the Plaid
    Link flow.

Example Code:

**1const { Configuration, PlaidApi, PlaidEnvironments } =
require(\'plaid\');**

**2**

**3const configuration = new Configuration({**

**4 basePath: PlaidEnvironments.sandbox,**

**5 baseOptions: {**

**6 headers: {**

**7 \'PLAID-CLIENT-ID\': process.env.PLAID_CLIENT_ID,**

**8 \'PLAID-SECRET\': process.env.PLAID_SECRET,**

**9 },**

**10 },**

**11});**

**12**

**13const client = new PlaidApi(configuration);**

**14**

**15app.post(\'/api/create_link_token\', async function (request,
response) {**

**16 const user = await User.find(\...);**

**17 const clientUserId = user.id;**

**18 const request = {**

**19 user: {**

**20 client_user_id: clientUserId,**

**21 },**

**22 client_name: \'Plaid Test App\',**

**23 products: \[\'auth\', \'identity\'\],**

**24 language: \'en\',**

**25 webhook: \'https://webhook.example.com\',**

**26 country_codes: \[\'US\'\],**

**27 };**

**28 try {**

**29 const createTokenResponse = await
client.linkTokenCreate(request);**

**30 response.json(createTokenResponse.data);**

**31 } catch (error) {**

**32 // Handle error**

**33 }**

**34});**

**35**

2\. Auth

The Auth product is used to retrieve bank account information necessary
for ACH transfers. This ensures that you have the correct account and
routing numbers for initiating payments.

Key Steps:

-   **Retrieve Account Information**: Use the **/auth/get** endpoint to
    get the user\'s account numbers.

Example Code:

**1app.post(\'/api/auth\', async function (request, response) {**

**2 const accessToken = request.body.access_token;**

**3 try {**

**4 const authResponse = await client.authGet({ access_token:
accessToken });**

**5 response.json(authResponse.data);**

**6 } catch (error) {**

**7 // Handle error**

**8 }**

**9});**

**10**

3\. Identity

The Identity product helps verify the ownership of the bank account,
reducing the risk of fraud. This is particularly useful for compliance
with KYC (Know Your Customer) regulations.

Key Steps:

-   **Verify Account Ownership**: Use the **/identity/get** endpoint to
    retrieve identity information.

Example Code:

**1app.post(\'/api/identity\', async function (request, response) {**

**2 const accessToken = request.body.access_token;**

**3 try {**

**4 const identityResponse = await client.identityGet({ access_token:
accessToken });**

**5 response.json(identityResponse.data);**

**6 } catch (error) {**

**7 // Handle error**

**8 }**

**9});**

**10**

4\. Transfer

The Transfer product allows you to send and receive money via ACH
transfers. This is essential for enabling instant payments to and from
your website.

Key Steps:

-   **Initiate Transfers**: Use
    the **/transfer/authorization/create** and **/transfer/create** endpoints
    to authorize and create transfers.

Example Code:

**1app.post(\'/api/transfer\', async function (request, response) {**

**2 const accessToken = request.body.access_token;**

**3 const accountId = request.body.account_id;**

**4 const amount = request.body.amount;**

**5**

**6 try {**

**7 // Create transfer authorization**

**8 const authorizationResponse = await
client.transferAuthorizationCreate({**

**9 access_token: accessToken,**

**10 account_id: accountId,**

**11 type: \'credit\',**

**12 network: \'ach\',**

**13 amount: amount,**

**14 ach_class: \'ppd\',**

**15 user: {**

**16 legal_name: \'John Doe\',**

**17 email_address: \'john.doe@example.com\',**

**18 },**

**19 });**

**20**

**21 // Create transfer**

**22 const transferResponse = await client.transferCreate({**

**23 authorization_id: authorizationResponse.data.authorization.id,**

**24 access_token: accessToken,**

**25 account_id: accountId,**

**26 type: \'credit\',**

**27 network: \'ach\',**

**28 amount: amount,**

**29 ach_class: \'ppd\',**

**30 description: \'Payment for services\',**

**31 user: {**

**32 legal_name: \'John Doe\',**

**33 email_address: \'john.doe@example.com\',**

**34 },**

**35 });**

**36**

**37 response.json(transferResponse.data);**

**38 } catch (error) {**

**39 // Handle error**

**40 }**

**41});**

**42**

Example Workflow

1.  **User Links Account**: User completes the Plaid Link flow.

2.  **Retrieve Account Info**: Use the Auth product to get account and
    routing numbers.

3.  **Verify Identity**: Use the Identity product to verify the account
    owner.

4.  **Initiate Transfer**: Use the Transfer product to send or receive
    payments.

Additional Considerations

To achieve your goal of allowing users to log into your website, link
their bank accounts, verify their identity, and enable instant payments,
you can leverage several Plaid products and endpoints. Here\'s a
step-by-step guide on how to implement this:

Step 1: User Authentication and Account Linking

First, you\'ll need to authenticate users and allow them to link their
bank accounts using Plaid Link.

Create a Link Token

Create a link token to initialize Plaid Link:

**1const request = {**

**2 user: { client_user_id: new Date().getTime().toString() },**

**3 client_name: \'Your App Name\',**

**4 products: \[\'auth\', \'identity\'\],**

**5 country_codes: \[\'US\'\],**

**6 language: \'en\',**

**7};**

**8**

**9try {**

**10 const response = await plaidClient.linkTokenCreate(request);**

**11 const linkToken = response.data.link_token;**

**12 // Pass linkToken to your frontend to initialize Plaid Link**

**13} catch (error) {**

**14 // Handle error**

**15}**

**16**

Step 2: Identity Verification

Once the user has linked their bank account, you can verify their
identity using the **/identity/get** endpoint.

Fetch Identity Data

**1const request = {**

**2 access_token: \'user-access-token\',**

**3};**

**4**

**5try {**

**6 const response = await plaidClient.identityGet(request);**

**7 const identityData = response.data;**

**8 // Compare identityData with your login information**

**9} catch (error) {**

**10 // Handle error**

**11}**

**12**

Step 3: Enable Instant Payments

If the identity verification is successful, you can proceed to enable
instant payments. For this, you can use Plaid\'s Payment Initiation
product.

Create a Payment Recipient

**1const recipientRequest = {**

**2 name: \'Recipient Name\',**

**3 iban: \'Recipient IBAN\',**

**4 address: {**

**5 street: \[\'Street Name\'\],**

**6 city: \'City\',**

**7 postal_code: \'Postal Code\',**

**8 country: \'Country\',**

**9 },**

**10};**

**11**

**12try {**

**13 const recipientResponse = await
plaidClient.paymentInitiationRecipientCreate(recipientRequest);**

**14 const recipientId = recipientResponse.data.recipient_id;**

**15 // Use recipientId to initiate payments**

**16} catch (error) {**

**17 // Handle error**

**18}**

**19**

Initiate a Payment

**1const paymentRequest = {**

**2 recipient_id: \'recipient-id\',**

**3 reference: \'Payment Reference\',**

**4 amount: {**

**5 currency: \'USD\',**

**6 value: 100.00,**

**7 },**

**8};**

**9**

**10try {**

**11 const paymentResponse = await
plaidClient.paymentInitiationPaymentCreate(paymentRequest);**

**12 const paymentId = paymentResponse.data.payment_id;**

**13 // Monitor payment status using webhooks or polling**

**14} catch (error) {**

**15 // Handle error**

**16}**

**17**

Additional Considerations

-   **Webhooks**: Set up webhooks to monitor the status of payments and
    handle any updates or errors.

-   **Security**: Ensure that all sensitive data is handled securely and
    in compliance with relevant regulations.

To achieve your goal of having users log in to your website, link their
bank accounts, and then automatically verify their identity in the
background, you can follow these steps using Plaid\'s API. Here\'s a
high-level overview of the process:

1.  **User Login and Bank Account Linking**:

    -   Use Plaid Link to allow users to link their bank accounts.

    -   Capture the **public_token** from Plaid Link and exchange it for
        an **access_token**.

2.  **Identity Verification**:

    -   Use the **/identity/get** endpoint to fetch identity data from
        the linked bank account.

    -   Match this identity data against the user\'s login information
        on your website.

3.  **Initiate Instant Payments**:

    -   Once the identity is verified, use the appropriate payment
        initiation endpoint to initiate payments to the linked bank
        account.

Step-by-Step Implementation

1\. User Login and Bank Account Linking

First, initialize Plaid Link and handle the **onSuccess** callback to
get the **public_token**.

**1const handler = Plaid.create({**

**2 token: \'GENERATED_LINK_TOKEN\',**

**3 onSuccess: async (public_token, metadata) =\> {**

**4 // Exchange the public_token for an access_token**

**5 const response = await fetch(\'/exchange_public_token\', {**

**6 method: \'POST\',**

**7 headers: {**

**8 \'Content-Type\': \'application/json\',**

**9 },**

**10 body: JSON.stringify({ public_token }),**

**11 });**

**12 const data = await response.json();**

**13 const access_token = data.access_token;**

**14**

**15 // Store the access_token securely**

**16 // Proceed to identity verification**

**17 verifyIdentity(access_token);**

**18 },**

**19 // Other Plaid Link configurations\...**

**20});**

**21**

2\. Identity Verification

Create a function to verify the user\'s identity by fetching identity
data from the linked bank account and matching it against the user\'s
login information.

**1async function verifyIdentity(access_token) {**

**2 const response = await fetch(\'/identity/get\', {**

**3 method: \'POST\',**

**4 headers: {**

**5 \'Content-Type\': \'application/json\',**

**6 },**

**7 body: JSON.stringify({ access_token }),**

**8 });**

**9 const identityData = await response.json();**

**10**

**11 // Match identity data with user\'s login information**

**12 const userLoginInfo = getUserLoginInfo(); // Implement this
function to get user\'s login info**

**13 const isMatch = matchIdentity(identityData, userLoginInfo); //
Implement this function to match identity**

**14**

**15 if (isMatch) {**

**16 // Initiate instant payment**

**17 initiatePayment(access_token);**

**18 } else {**

**19 console.error(\'Identity verification failed\');**

**20 }**

**21}**

**22**

3\. Initiate Instant Payments

Create a function to initiate payments to the linked bank account.

**1async function initiatePayment(access_token) {**

**2 const response = await fetch(\'/payment/initiate\', {**

**3 method: \'POST\',**

**4 headers: {**

**5 \'Content-Type\': \'application/json\',**

**6 },**

**7 body: JSON.stringify({ access_token, amount: 1000 }), // Example
amount**

**8 });**

**9 const paymentResult = await response.json();**

**10**

**11 if (paymentResult.success) {**

**12 console.log(\'Payment initiated successfully\');**

**13 } else {**

**14 console.error(\'Payment initiation failed\');**

**15 }**

**16}**

**17**

Backend Endpoints

You will need to implement the backend endpoints to handle the token
exchange, identity fetching, and payment initiation.

Exchange Public Token

**1@app.route(\'/exchange_public_token\', methods=\[\'POST\'\])**

**2def exchange_public_token():**

**3 public_token = request.json\[\'public_token\'\]**

**4 response = plaid_client.Item.public_token.exchange(public_token)**

**5 access_token = response\[\'access_token\'\]**

**6 return jsonify({\'access_token\': access_token})**

**7**

Fetch Identity Data

**1@app.route(\'/identity/get\', methods=\[\'POST\'\])**

**2def get_identity():**

**3 access_token = request.json\[\'access_token\'\]**

**4 response = plaid_client.Identity.get(access_token)**

**5 return jsonify(response)**

**6**

Initiate Payment

**1@app.route(\'/payment/initiate\', methods=\[\'POST\'\])**

**2def initiate_payment():**

**3 access_token = request.json\[\'access_token\'\]**

**4 amount = request.json\[\'amount\'\]**

**5 \# Implement payment initiation logic here**

**6 return jsonify({\'success\': True})**

**7**

Conclusion

By following these steps, you can allow users to log in, link their bank
accounts, verify their identity automatically, and initiate instant
payments. Make sure to handle errors and edge cases appropriately in
your implementation. For more detailed information, refer to the [[Plaid
API documentation]{.underline}](https://plaid.com/docs/).

To facilitate the process of linking bank accounts, verifying identity,
and initiating payments on your website using Plaid, you\'ll need to
understand how to implement webhooks and ensure security. Below is a
comprehensive guide covering webhooks, security best practices, and
other necessary details.

Webhooks

What are Webhooks?

A webhook is an HTTP request used to provide push notifications. Plaid
uses webhooks to programmatically inform you about changes to Plaid
Items or the status of asynchronous processes.

Why Use Webhooks?

-   **Real-time Updates**: Webhooks provide real-time updates about the
    status of various processes.

-   **Handling Asynchronous Processes**: Some processes in Plaid are
    asynchronous and may take some time to complete. Webhooks notify
    your application when these processes are complete.

-   **Error Handling**: If an Item enters an error state, a webhook can
    notify your application so that you can take appropriate action.

-   **New Account Notifications**: When a user adds a new account, you
    will receive a **NEW_ACCOUNTS_AVAILABLE** webhook.

Configuring Webhooks

Webhooks are typically configured via the **webhook** parameter
of **/link/token/create**. For example:

**1{**

**2 \"client_id\": \"your_client_id\",**

**3 \"secret\": \"your_secret\",**

**4 \"user\": {**

**5 \"client_user_id\": \"unique_user_id\"**

**6 },**

**7 \"webhook\": \"https://yourdomain.com/webhook\",**

**8 \"products\": \[\"auth\", \"identity\"\],**

**9 \"country_codes\": \[\"US\"\],**

**10 \"language\": \"en\"**

**11}**

**12**

Handling Webhooks

Set up a dedicated endpoint on your server to receive POST requests from
Plaid. Here\'s an example in Python using Flask:

**1@app.route(\'/webhook\', methods=\[\'POST\'\])**

**2def webhook():**

**3 webhook_data = request.json**

**4 \# Process the webhook data**

**5 return \'\', 200**

**6**

Testing Webhooks

You can test webhooks in the Sandbox environment using
the **/sandbox/item/fire_webhook** endpoint:

**1response = plaid_client.Sandbox.item.fire_webhook({**

**2 \'access_token\': \'your_access_token\',**

**3 \'webhook_code\': \'DEFAULT_UPDATE\'**

**4})**

**5**

Security Best Practices

Secure Your Webhook Endpoint

-   **HTTPS**: Ensure your webhook endpoint uses HTTPS to encrypt data
    in transit.

-   **IP Whitelisting**: Plaid sends webhooks from specific IP
    addresses. You can whitelist these IPs to ensure requests are coming
    from Plaid.

    -   52.21.26.131

    -   52.21.47.157

    -   52.41.247.19

    -   52.88.82.239

-   **Webhook Verification**: Verify that the webhook requests are from
    Plaid by checking the request headers and payload.

Handle Duplicate and Out-of-Order Webhooks

Design your application to handle duplicate and out-of-order webhooks.
Ensure idempotency on actions you take when receiving a webhook.

Secure API Keys

-   **Environment Variables**: Store your API keys in environment
    variables, not in your source code.

-   **Access Controls**: Limit access to your API keys to only those who
    need it.

Rate Limiting

Implement rate limiting to protect your application from being
overwhelmed by too many requests in a short period.

Additional Information

Identity Verification

Use the **/identity/get** endpoint to fetch identity data from the
linked bank account and match it against the user\'s login information.

**1@app.route(\'/identity/get\', methods=\[\'POST\'\])**

**2def get_identity():**

**3 access_token = request.json\[\'access_token\'\]**

**4 response = plaid_client.Identity.get(access_token)**

**5 return jsonify(response)**

**6**

Initiate Payments

Once the identity is verified, use the appropriate payment initiation
endpoint to initiate payments to the linked bank account.

**1@app.route(\'/payment/initiate\', methods=\[\'POST\'\])**

**2def initiate_payment():**

**3 access_token = request.json\[\'access_token\'\]**

**4 amount = request.json\[\'amount\'\]**

**5 \# Implement payment initiation logic here**

**6 return jsonify({\'success\': True})**

**7**

Comprehensive List of Webhooks for Error Handling and Other Processes

Below is a detailed list of webhooks that you might need for error
handling and other processes when using Plaid. Each webhook includes a
brief description and an example payload.

1. **NEW_ACCOUNTS_AVAILABLE** Webhook

Triggered when a user adds a new account.

Webhook Payload

**1{**

**2 \"webhook_type\": \"ITEM\",**

**3 \"webhook_code\": \"NEW_ACCOUNTS_AVAILABLE\",**

**4 \"item_id\": \"item_id_string\",**

**5 \"new_accounts\": \[**

**6 {**

**7 \"account_id\": \"account_id_string\",**

**8 \"mask\": \"0000\",**

**9 \"name\": \"Plaid Checking\",**

**10 \"official_name\": \"Plaid Gold Standard 0% Interest Checking\",**

**11 \"subtype\": \"checking\",**

**12 \"type\": \"depository\"**

**13 }**

**14 \]**

**15}**

**16**

2. **ERROR** Webhook

Triggered when an Item enters an error state.

Webhook Payload

**1{**

**2 \"webhook_type\": \"ITEM\",**

**3 \"webhook_code\": \"ERROR\",**

**4 \"item_id\": \"item_id_string\",**

**5 \"error\": {**

**6 \"error_type\": \"ITEM_ERROR\",**

**7 \"error_code\": \"ITEM_LOGIN_REQUIRED\",**

**8 \"error_message\": \"the login details for this Item have
changed\",**

**9 \"display_message\": \"Your login details have changed. Please
update them.\",**

**10 \"documentation_url\":
\"https://plaid.com/docs/errors/item/#ITEM_LOGIN_REQUIRED\",**

**11 \"suggested_action\": \"Prompt the user to re-enter their login
credentials.\"**

**12 }**

**13}**

**14**

3. **DEFAULT_UPDATE** Webhook

Triggered when there is an update to the Item.

Webhook Payload

**1{**

**2 \"webhook_type\": \"ITEM\",**

**3 \"webhook_code\": \"DEFAULT_UPDATE\",**

**4 \"item_id\": \"item_id_string\"**

**5}**

**6**

4. **TRANSACTIONS_REMOVED** Webhook

Triggered when transactions are removed from an Item.

Webhook Payload

**1{**

**2 \"webhook_type\": \"TRANSACTIONS\",**

**3 \"webhook_code\": \"TRANSACTIONS_REMOVED\",**

**4 \"item_id\": \"item_id_string\",**

**5 \"removed_transactions\": \[\"transaction_id_1\",
\"transaction_id_2\"\]**

**6}**

**7**

5. **HISTORICAL_UPDATE** Webhook

Triggered when the initial batch of historical transactions is
available.

Webhook Payload

**1{**

**2 \"webhook_type\": \"TRANSACTIONS\",**

**3 \"webhook_code\": \"HISTORICAL_UPDATE\",**

**4 \"item_id\": \"item_id_string\",**

**5 \"new_transactions\": 100**

**6}**

**7**

6. **SYNC_UPDATES_AVAILABLE** Webhook

Triggered when new transaction data is available for
the **/transactions/sync**endpoint.

Webhook Payload

**1{**

**2 \"webhook_type\": \"TRANSACTIONS\",**

**3 \"webhook_code\": \"SYNC_UPDATES_AVAILABLE\",**

**4 \"item_id\": \"item_id_string\",**

**5 \"new_transactions\": 10**

**6}**

**7**

7. **PRODUCT_READY** Webhook

Triggered when an asset report is ready to be retrieved.

Webhook Payload

**1{**

**2 \"webhook_type\": \"ASSETS\",**

**3 \"webhook_code\": \"PRODUCT_READY\",**

**4 \"item_id\": \"item_id_string\",**

**5 \"asset_report_id\": \"asset_report_id_string\"**

**6}**

**7**

8. **INCOME_VERIFICATION** Webhook

Triggered when income verification is complete.

Webhook Payload

**1{**

**2 \"webhook_type\": \"INCOME\",**

**3 \"webhook_code\": \"INCOME_VERIFICATION\",**

**4 \"item_id\": \"item_id_string\",**

**5 \"verification_status\": \"VERIFIED\"**

**6}**

**7**

9. **IDENTITY_VERIFICATION** Webhook

Triggered when identity verification is complete.

Webhook Payload

**1{**

**2 \"webhook_type\": \"IDENTITY_VERIFICATION\",**

**3 \"webhook_code\": \"IDENTITY_VERIFICATION\",**

**4 \"item_id\": \"item_id_string\",**

**5 \"verification_status\": \"VERIFIED\"**

**6}**

**7**

10. **PAYMENT_STATUS_UPDATE** Webhook

Triggered to keep updated on payment status information.

Webhook Payload

**1{**

**2 \"webhook_type\": \"PAYMENT_INITIATION\",**

**3 \"webhook_code\": \"PAYMENT_STATUS_UPDATE\",**

**4 \"payment_id\": \"payment_id_string\",**

**5 \"status\": \"INITIATED\"**

**6}**

**7**

Conclusion

These are some of the key webhooks you might need for error handling and
other processes when using Plaid. Each webhook provides specific
information about the status of various processes, allowing you to
handle them appropriately in your application. For a full list of
webhooks and detailed information, refer to the [[Plaid Webhooks
Documentation]{.underline}](https://plaid.com/docs/api/webhooks/).

RF
