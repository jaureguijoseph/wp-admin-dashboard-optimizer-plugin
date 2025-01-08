> **Accept.js**

Accept.js is a JavaScript library for sending secure payment data
directly to Authorize.net. Accept.js captures the payment data and
submits it directly to us, in exchange for a one-time-use token, or
payment nonce. You can use this payment nonce in the place of payment
data in a follow-on

[createTransactionRequest]{.underline}

[(https://developer.authorize.net/api/reference/#accept-suite-create-anaccept-payment-transaction)]{.underline}
API call.

Our JavaScript library offers developers two work flow options for
accepting payment:

> **Option 1:** Host [your own payment form]{.underline} for a PCI-DSS
> SAQ A-EP solution that gives you complete control of the customer
> payment experience.
>
> **Option 2:** Embed our hosted, mobile-optimized [payment information
> form]{.underline} in your page to collect the card information in a
> PCI-DSS SAQ A compliant way.

For a fully hosted payment solution that you can redirect your customers
to or embed as an iFrame within your page, see our documentation for
[Accept Hosted
(http://developer.authorize.net/api/reference/features/accept-hosted.html)]{.underline}.

**Resources API Reference**

> **(http://developer.authorize.net/api/refer**
>
> **Sample Application on GitHub**
>
> **(https://github.com/AuthorizeNet/accept**
>
> **sample-app) Looking for AIM/SIM?**
>
> **(http://developer.authorize.net/api/upgra**

# Generating and Using the Public Client Key

Before using Accept.js, you must generate a Public Client Key. There are
two ways to generate the Public Client Key: using the API or the
Merchant Interface.

## Generating the Public Client Key Using the API

Call the [getMerchantDetailsRequest]{.underline}

[(https://developer.authorize.net/api/reference/index.html#transactionreporting-get-merchant-details)]{.underline}
API request. The response contains the \"eld

> publicClientKey , which contains the value of the key.
>
> **NOTE:** If you are a merchant, use your login ID and API Key to call
> [getMerchantDetailsRequest]{.underline}

[(https://developer.authorize.net/api/reference/index.html#transactionreporting-get-merchant-details)]{.underline}
. If you are a partner submitting this request on a merchant\'s behalf,
use your [OAuth]{.underline}

[(https://developer.authorize.net/api/reference/features/oauth.html)]{.underline}
credentials.

## Generating the Public Client Key using the Merchant Interface

To generate the Client Key, log in to the Merchant Interface as an
Administrator and navigate to **Account \> Settings \> Security Settings
\> General Security Settings \> Manage Public Client Key**. If the
Public Client Key does not yet exist, answer your security question to
generate the key.

## Using the Public Client Key

Copy the Public Client Key into the client-side code, as shown in
examples throughout this page. Because you cannot use the Public Client
Key to initate a transaction, you may safely store the Public Client Key
in a website or smartphone application, for example.

# Workflow

## Option 1: Using Your Own Payment Form

![](media/image1.png){width="5.0in" height="1.7708333333333333in"}

When using Accept.js in your payment form, follow the below work!ow:

1.  Use Accept.js to receive the payment details from your form and send
    themfrom the customer\'s browser to Authorize.net, which returns a
    payment nonce along with the order details.

2.  Temporarily store the payment nonce returned in Step 1.

3.  Send a [createTransactionRequest]{.underline}

[(https://developer.authorize.net/api/reference/#accept-suite-create-anaccept-payment-transaction)]{.underline}
API call to us, using the payment nonce instead of the customer\'s
payment information.

## Option 2: Using the Hosted Payment Information Form

When using the Accept.js hosted payment information form, follow the
below work!ow:

1.  Add the Accept.js library to a page on your site.

2.  Using the HTML form tag, create a submit button on the page with the
    Accept.js library. Set the action parameter of the form to point at
    your response handler script, and con\"gure the button to interact
    with the AcceptUI portion of the Accept.js library. The customer
    will click the button to open the payment information form.

3.  During checkout, the customer clicks the button to open the
    paymentinformation form. The customer \"lls out and submits the
    form.

4.  The Accept script sends the payment information directly to
    Authorize.net andreceives a payment nonce. The script passes the
    payment nonce to the response handler function that you speci\"ed
    when you de\"ned the button in step 1.

5.  Your response handler sends the nonce and any other necessary
    informationfrom your page back to your server, which sends a
    transaction request (for

example [createTransactionRequest]{.underline}

[(https://developer.authorize.net/api/reference/#accept-suite-create-anaccept-payment-transaction)]{.underline}
) to Authorize.net using the payment nonce in place of the account
number and other payment information.

# Integrating the JavaScript Library into Your Page

The appropriate Accept JavaScript library must be integrated into your
page so that the sensitive data is intercepted and replaced with the
payment nonce before the form contents are submitted to your server.

The Accept JavaScript libraries are hosted by Authorize.net and must be
loaded by your site from the Authorize.net servers. The libaries are
unique to both Authorize.net environments (production and sandbox), so
it is important to load the sandbox library if you are testing
transactions, and to use the production library for live transaction
processing, as shown below.

## Loading the JavaScript Library (with Your Own Payment Form)

**Sandbox**

\<script type=\"text/javascript\"

**Copy**

> src=\"https://jstest.authorize.net/v1/Accept.js\" charset=\"utf-8\"\>
>
> \</script\>

**Production**

> \<script type=\"text/javascript\"
> src=\"https://js.authorize.net/v1/Accept.js\" charset=\"utf-8\"\>

**Copy**

> \</script\>

## Loading the JavaScript Library (with the Hosted Form)

**Sandbox**

\<script type=\"text/javascript\"

**Copy**

> src=\"https://jstest.authorize.net/v3/AcceptUI.js\"
> charset=\"utf-8\"\>
>
> \</script\>

**Production**

> \<script type=\"text/javascript\"
> src=\"https://js.authorize.net/v3/AcceptUI.js\" charset=\"utf-8\"\>

**Copy**

> \</script\>

**IMPORTANT:** When using the payment information form, be sure that
your page loads the library after the button is de\"ned.

# Building your Payment Form

When you build Accept.js into your own payment form you have complete
control over the design and !ow. You are only required to identify the
elements in the payment form that you wish to process with Accept.js.
This usually means that you only need to know the ID attributes of those
input elements.

When you integrate the hosted payment information form into your payment
form (using AcceptUI.js), you control everything but the \"elds for card
number, expiration date, card code, and optionally, name and postal
(ZIP) code. That information is collected on the payment form which pops
up when the customer clicks the button you\'ve de\"ned for that purpose.

**Important:** If you use the hosted payment information form\'s postal
(ZIP) code \"eld, the Accept.js nonce will include the postal code, in
which case the nonce\'s postal code will be used in Address Veri\"cation
Service (AVS) checks. Optionally, you can pass the nonce along with the
customer\'s billing address in your

## [createTransactionRequest (https://developer.authorize.net/api/reference/#payment-]{.underline}

[transactions)]{.underline} API call, in which case the postal code
included in the API call will be used in AVS checks instead. For AVS
checks, the postal code used with

> createTransactionRequest takes precedence over the value in the nonce.

# Integrating Accept.js into Your Payment Form

## Form Construction

When building Accept.js into your own payment form, you are only
required to identify the elements in the payment form that you will
process with Accept.js. This usually means that you only need to know
the ID attributes of those input elements.

Note that Accept.js handles card expiration dates as two distinct values
for month and year. Additionally, the cardholder name is treated as a
single \"eld without breaking it up into \"rst name and last name. If
you are designing a new form, it will simplify your integration to keep
these formats in mind.

By way of example, we\'ll start with this simple HTML form. The hidden
\"elds will be used later to hold the payment nonce values returned by
Authorize.net.

> \<form id=\"paymentForm\" method=\"POST\"

**Copy**

> action=\"https://YourServer/PathToExistingPaymentProcessingScript\"\>
> \<input type=\"text\" name=\"cardNumber\" id=\"cardNumber\"
> placeholder=\"cardN
>
> \<input type=\"text\" name=\"expMonth\" id=\"expMonth\"
> placeholder=\"expMonth
>
> \<input type=\"text\" name=\"expYear\" id=\"expYear\"
> placeholder=\"expYear\"/\> \<br
>
> \<input type=\"text\" name=\"cardCode\" id=\"cardCode\"
> placeholder=\"cardCode
>
> \<input type=\"hidden\" name=\"dataValue\" id=\"dataValue\" /\>
>
> \<input type=\"hidden\" name=\"dataDescriptor\" id=\"dataDescriptor\"
> /\>
>
> \<button\>Pay\</button\>
>
> \</form\>

## Intercepting the Form Submission

To prevent the secure data from reaching your server, the form
submission must be intercepted so that the secure details can be
transmitted to Authorize.net and replaced with a payment nonce (a
single-use token). Many commonly used libraries include helper functions
to perform an action before a form is posted and you can feel free to
use those, but for basic HTML the easist way to do this is to capture a
user clicking the submit button.

For our sample HTML payment form above, we would replace line number 10
with the following. The sendPaymentDataToAnet() method will be de\"ned
in the next section.

> \<form id=\"paymentForm\" method=\"POST\"

**Copy**

> action=\"https://YourServer/PathToExistingPaymentProcessingScript\" \>
> \<input type=\"text\" name=\"cardNumber\" id=\"cardNumber\"
> placeholder=\"cardN
>
> \<input type=\"text\" name=\"expMonth\" id=\"expMonth\"
> placeholder=\"expMonth
>
> \<input type=\"text\" name=\"expYear\" id=\"expYear\"
> placeholder=\"expYear\"/\> \<br
>
> \<input type=\"text\" name=\"cardCode\" id=\"cardCode\"
> placeholder=\"cardCode
>
> \<input type=\"hidden\" name=\"dataValue\" id=\"dataValue\" /\>
>
> \<input type=\"hidden\" name=\"dataDescriptor\" id=\"dataDescriptor\"
> /\>
>
> \<button type=\"button\"
> onclick=\"sendPaymentDataToAnet()\"\>Pay\</button\> \</form\>

## Dispatching Data to Authorize.net

Accept.js includes a method to submit the secure data to Authorize.net,
but before that can be done, use the secure data to populate the
required objects. At a minimum, the method to submit the data requires
an authentication object ( authData ) and either a card data object (
cardData ) or a bank data object ( bankData ).

## Building the Authentication Object

The authentication data object includes only two values, your API Login
ID and the Public Client Key that was referenced above. The content of
this object is used to tie the payment nonce to your individual
Authorize.net account. This data should be added to an object in your
own script on the page.

\<script type=\"text/javascript\"\>

**Copy**

> function sendPaymentDataToAnet() { var authData = {};
>
> authData.clientKey = \"YOUR PUBLIC CLIENT KEY\"; authData.apiLoginID =
> \"YOUR API LOGIN ID\"; }
>
> \</script\>

## Building the Card Data Object

The card data object includes all of the payment details that will be
replaced by the payment nonce. At a minimum, this includes the card
number and expiration date. In the case of our basic sample form, it
would also include the card code. As with the authentication data, this
should be added to an object.

\<script type=\"text/javascript\"\>

**Copy**

> function sendPaymentDataToAnet() { var authData = {};
>
> authData.clientKey = \"YOUR PUBLIC CLIENT KEY\"; authData.apiLoginID =
> \"YOUR API LOGIN ID\";
>
> var cardData = {};
>
> cardData.cardNumber = document.getElementById(\"cardNumber\").value;
> cardData.month = document.getElementById(\"expMonth\").value;
> cardData.year = document.getElementById(\"expYear\").value;
> cardData.cardCode = document.getElementById(\"cardCode\").value; }
>
> \</script\>

The complete list of \"elds that can be inserted into the card data
object are:

## Card Data Properties

**Property**

**Data**

**Type**

**Description**

**Required**

cardNumber

String

Must be a valid 13-16 digit card

number.

Yes

month

String

2-

digit month.

Yes

year

String

2-

digit year.

Yes

cardCode

String

3

or 4-digit Card Code

No

zip

String

Alphanumeric postal code. 20-

character maximum.

No

fullName

String

Alphanumeric cardholder

name. 64-character maximum.

No

> name. 64-character maximum.

Accept.js contains built-in data type validation for these \"elds. While
Accept.js validates the data, it doesn't do any authorization of the
card number or any check to see if the expiration date, postal code, or
card code are correct for that card. Those steps will happen later in
the process when you submit the payment nonce as part of a transaction
request.

## Building a Bank Data Object

As an alternative to using card data as shown above, you can also
generate a payment nonce using bank account information for [eCheck
transactions]{.underline}

[(https://developer.authorize.net/api/reference/features/echeck.html)]{.underline}.
Use the bankData element to create an object for bank account
information as shown

below.

\<script type=\"text/javascript\"\>

**Copy**

> function sendPaymentDataToAnet() { var authData = {};
>
> authData.clientKey = \"YOUR PUBLIC CLIENT KEY\"; authData.apiLoginID =
> \"YOUR API LOGIN ID\";
>
> var bankData = {};
>
> bankData.accountNumber = document.getElementById(\'accountNumber\').v
> bankData.routingNumber =
> document.getElementById(\'routingNumber\').val bankData.nameOnAccount
> = document.getElementById(\'nameOnAccount\') bankData.accountType =
> document.getElementById(\'accountType\').value; }
>
> \</script\>

The complete list of \"elds that can be inserted into bankData are:

## Bank Data Properties

**Property**

**Data Type**

**Description**

accountNumber

Numeric string.

Up to 17 digits.

The bank account number.

routingNumber

Numeric string.

9

digits.

The ABA routing number.

nameOnAccount

String.

Up to 22

characters.

The name of the person

who holds the bank

account.

accountType

String.

Either

checking

,

savings

, or

businessChecking

.

The type of bank account

used for the eCheck.Net

transaction.

**When sending**

bankData

**all**

**fi**

**elds are required.**

**Running the Dispatch Data Method**

The dispatchData() method takes two inputs. One is an object which
collects both the authentication and payment data. The other is the
JavaScript method that you will create to handle the Accept.js response.
This method can either be de\"ned inline, or can be referenced by name
from elsewhere in your script. The response handler function will be
created in the next section, but you can see it here referred to simply
as responseHandler .

\<script type=\"text/javascript\"\>

**Copy**

> function sendPaymentDataToAnet() { var authData = {};
>
> authData.clientKey = \"YOUR PUBLIC CLIENT KEY\"; authData.apiLoginID =
> \"YOUR API LOGIN ID\";
>
> var cardData = {};
>
> cardData.cardNumber = document.getElementById(\"cardNumber\").value;
> cardData.month = document.getElementById(\"expMonth\").value;
> cardData.year = document.getElementById(\"expYear\").value;
> cardData.cardCode = document.getElementById(\"cardCode\").value;
>
> // If using banking information instead of card information, // build
> a bankData object instead of a cardData object.
>
> //
>
> // var bankData = {};
>
> // bankData.accountNumber = document.getElementById(\'accountNumber

// bankData.routingNumber = document.getElementById(\'routingNumber\').v
The code above represents the complete sendPaymentDataToAnet() function
which was previously set to be executed upon clicking the submit button.

[Continue to the Response Handling Section]{.underline}

> **Integrating the Hosted Payment Information**

# Form

## Form Construction

When you use the hosted payment information form, your own payment form
can be simpler, collecting only the non-payment information that you
need. The only required element is a button to launch the payment
information form. You could choose to have that button also act as the
submit button for your form by coding your response handler function to
submit the rest of the information in your form once you\'ve received
the payment nonce. Alternatively, you could set up your response handler
to return control to your form allowing the customer to \"ll or edit
more information before clicking a different submit button to pass all
of the information to your server.

In the following example, we use a simple HTML form. We will use the
hidden \"elds later to hold the payment nonce values returned by
Authorize.net.

> \<form id=\"paymentForm\" method=\"POST\"

**Copy**

> action=\"https://YourServer/PathToExistingPaymentProcessingScript\"\>
> \<input type=\"hidden\" name=\"dataValue\" id=\"dataValue\" /\>
>
> \<input type=\"hidden\" name=\"dataDescriptor\" id=\"dataDescriptor\"
> /\>
>
> \<button type=\"button\"\>Pay\</button\>
>
> \</form\>

The result will be a lightbox containing the payment information form,
similar to the one below.

![](media/image2.jpg){width="3.8472222222222223in"
height="2.7222222222222223in"}

**Important:** If you use the hosted payment information form\'s postal
(ZIP) code \"eld, the Accept.js nonce will include the postal code, in
which case the nonce\'s postal code will be used in Address Veri\"cation
Service (AVS) checks. Optionally, you can pass the nonce along with the
customer\'s billing address in your

### createTransactionRequest (https://developer.authorize.net/api/reference/#payment-

[transactions)]{.underline} API call, in which case the postal code
included in the API call will be used in AVS checks instead. For AVS
checks, the postal code used with createTransactionRequest takes
precedence over the value in the nonce.

## Adding the Payment Information Form Button to Your Page

You can style the button in any way you like, however, the button must
be a member of the AcceptUI class. In addition to the class attribute, a
set of required data attributes are used to con\"gure the card entry
form.

> \<form id=\"paymentForm\" method=\"POST\"

**Copy**

> action=\"https://YourServer/PathToExistingPaymentProcessingScript\"\>
> \<input type=\"hidden\" name=\"dataValue\" id=\"dataValue\" /\>
>
> \<input type=\"hidden\" name=\"dataDescriptor\" id=\"dataDescriptor\"
> /\>
>
> \<button type=\"button\" class=\"AcceptUI\"
>
> data-billingAddressOptions=\'{\"show\":true, \"required\":false}\'
> data-apiLoginID=\"YOUR API LOGIN ID\" data-clientKey=\"YOUR PUBLIC
> CLIENT KEY\" data-acceptUIFormBtnTxt=\"Submit\"
> data-acceptUIFormHeaderTxt=\"Card Information\"
>
> data-paymentOptions=\'{\"showCreditCard\": true, \"showBankAccount\":
> true data-responseHandler=\"responseHandler\"\>Pay
>
> \</button\>
>
> \</form\>

**Button Input Parameters**

**Parameter**

**Type**

**Description**

data-

billingAddressOptions

JSON-formatted

string

Elements of this

parameter include:

show

\- Boolean

indicating whether the

name and postal code

\"

elds should be shown

on the form. The

default is

true

.

required

\- Boolean

indicating whether the

name and postal code

\"

elds are required to

> \"elds are required to

+-------------------------+------------------+------------------------+
|                         |                  | > be \"lled in. The    |
|                         |                  | > default is true .    |
+=========================+==================+========================+
| > data-apiLoginID       | String           | > API Login ID of the  |
|                         |                  | > merchant. It can be  |
|                         |                  | > found in the         |
|                         |                  | >                      |
|                         |                  | > Authorize.net        |
|                         |                  | >                      |
|                         |                  | > Merchant Interface   |
|                         |                  | > at                   |
|                         |                  | >                      |
|                         |                  | > **Account \>         |
|                         |                  | > Settings \>**        |
|                         |                  | >                      |
|                         |                  | > **Security Settings  |
|                         |                  | > \>**                 |
|                         |                  | >                      |
|                         |                  | > **General Security** |
|                         |                  | >                      |
|                         |                  | > **Settings \> API**  |
|                         |                  | >                      |
|                         |                  | > **Credentials and    |
|                         |                  | > Keys**. Required.    |
+-------------------------+------------------+------------------------+
| > data-clientKey        | String           | > Public Client Key    |
|                         |                  | > for the merchant. It |
|                         |                  | > can be generated in  |
|                         |                  | > the Authorize.net    |
|                         |                  | >                      |
|                         |                  | > Merchant Interface   |
|                         |                  | > at                   |
|                         |                  | >                      |
|                         |                  | > **Account \>         |
|                         |                  | > Settings \>**        |
|                         |                  | >                      |
|                         |                  | > **Security Settings  |
|                         |                  | > \>**                 |
|                         |                  | >                      |
|                         |                  | > **General Security** |
|                         |                  | >                      |
|                         |                  | > **Settings \> Manage |
|                         |                  | > Public Client Key**. |
|                         |                  | >                      |
|                         |                  | > Required.            |
+-------------------------+------------------+------------------------+
| >                       | String           | > This is the text     |
|  dataacceptUIFormBtnTxt |                  | > that is displayed on |
|                         |                  | > the button. The      |
|                         |                  | > default is Pay .     |
+-------------------------+------------------+------------------------+
| > da                    | String           | > This is the text     |
| taacceptUIFormHeaderTxt |                  | > that is displayed on |
|                         |                  | > the header of the    |
|                         |                  | > payment form. The    |
|                         |                  | > default is to not    |
|                         |                  | > display any text.    |
+-------------------------+------------------+------------------------+
| > data-paymentOptions   | JSON-formatted   | > This attribute       |
|                         | string           | > contains:            |
|                         |                  | >                      |
|                         |                  | > showCreditCard       |
|                         |                  | > Boolean indicating   |
|                         |                  | > whether the Card     |
|                         |                  | > Data                 |
+-------------------------+------------------+------------------------+
|                         |                  | > \"elds should be     |
|                         |                  | > shown on the form.   |
|                         |                  | > The default is true  |
|                         |                  | > .                    |
|                         |                  | >                      |
|                         |                  | > showBankAccount      |
|                         |                  | > Boolean indicating   |
|                         |                  | > whether the Bank     |
|                         |                  | > Account Data \"elds  |
|                         |                  | > should be shown on   |
|                         |                  | > the form. The        |
|                         |                  | > default is false .   |
+-------------------------+------------------+------------------------+
| > data-responseHandler  | responseHandler  | > Speci\"es the        |
|                         |                  | > responseHandler      |
|                         |                  | >                      |
|                         |                  | > JavaScript function. |
+-------------------------+------------------+------------------------+

You don\'t need to specify an onClick handler or anything else to allow
the button to activate the payment form. The button's membership in the
AcceptUI class allows the script to listen for the button's onClick
event.

> **If you are building your page dynamically, be sure that your page
> loads the Accept JavaScript library after the button is defined. For
> the library to correctly interface with the button, the button must
> already exist in the DOM when the library is loaded.**

# Handling the Response

You must create a JavaScript function to handle the response from
Authorize.net. This function will receive an object containing the
request status as well as the payment nonce (in the case of a successful
request).

**Response Parameters**

**Property**

**Type**

**Description**

opaqueData.dataDescriptor

String

This value must be

passed to the

Authorize.net API, along

with

dataValue

, to

represent the card

details.

opaqueData.dataValue

String

This value is the payment

nonce that you received

from Authorize.net. This

value must be passed to

the Authorize.net API,

along with

dataDescriptor

, to

represent the card

details. The nonce is valid

for 15 minutes.

encryptedCardData.cardNumber

String

This is only returned

when using the hosted

form. Last four digits of

the card.

encryptedCardData.expDate

String

This is only returned

when using the hosted

form. Expiration date

submitted with the card.

encryptedCardData.bin

String

This is only returned

when using the hosted

form. Issuer

Identi

\"

cation Number

(

\"

rst six digits of the

card number).

customerInformation.

\"

rstName

String

This is only returned

when using the hosted

form. Customer

\"

rst

name submitted with the

card.

customerInformation.lastName

String

This is only returned

when using the hosted

form. Customer last

name submitted with the

card.

A complete response from the Authorize.net servers will look something
like the code samples below. These particular examples represents a
successful request.

**Hosted Form Sample Response Object**

> {
>
> \"opaqueData\": {
>
> \"dataDescriptor\": \"COMMON.ACCEPT.INAPP.PAYMENT\",
>
> \"dataValue\": \"eyJjb2RlIjoiNTBfMl8wNjAwMDUzNUE1OTkzREQ1NEM1NzY0OTgwN
>
> },
>
> \"messages\": {
>
> \"resultCode\": \"Ok\",
>
> \"message\": \[{
>
> \"code\": \"I_WC_01\",
>
> \"text\": \"Successful.\"
>
> }\]
>
> },
>
> \"encryptedCardData\": {

\"cardNumber\": \"XXXXXXXXXXXX1111\", **API Hello world**

> \"expDate\": \"12/22\",
>
> \"bin\": \"411111\"
>
> },

**Non-Hosted Form Sample Response Object**

{

**Copy**

> \"opaqueData\":{
>
> \"dataDescriptor\":\"COMMON.ACCEPT.INAPP.PAYMENT\",
>
> \"dataValue\":\"9487801666614876704604\"
>
> },
>
> \"messages\":{
>
> \"resultCode\":\"Ok\",
>
> \"message\":\[
>
> {
>
> \"code\":\"I_WC_01\",
>
> \"text\":\"Successful.\"
>
> }
>
> \]
>
> }
>
> }

## Examining the Response for Accept.js Request Status

The messages block of the response identi\"es the result of your request
for tokenization through the Accept.js library. The resultCode element
will have a status of either Ok or Error , while the message\'s code and
text will provide more meaningful details in the case of a failure. A
full list of error codes is provided in the [Appendix]{.underline}. For
your responseHandler function, you will want to start by checking if the
result was an error.

> function responseHandler(response) { if (response.messages.resultCode
> === \"Error\") { var i = 0;
>
> while (i \< response.messages.message.length) { console.log(
>
> response.messages.message\[i\].code + \": \" +
>
> response.messages.message\[i\].text
>
> ); i = i + 1;
>
> }
>
> }
>
> }

## Examining the Response for Card Information (Hosted Form Only)

When you use the hosted form, the encryptedCardData block of the
response will contain non-sensitive information about the card which you
may want to use to display before completing the transaction. The
customerInformation object contains the cardholder name and postal code
if captured in the form. Other information, such as opaqueData is just
the same as when not using the hosted form version of the library.

## Capturing the Opaque Data

When a request is successful, your script will receive the payment nonce
in the dataValue element of the opaqueData object. In the case of a
successful

response, you must capture the value of dataValue . The nonce is valid
for 15 minutes.

Your server can call the [Authorize.net API]{.underline}

[(https://developer.authorize.net/api/reference/#accept-suite-create-anaccept-payment-transaction)]{.underline}
to actually process the transaction just like you would if you were
using actual card information.The dataDescriptor and dataValue will be
entered into the payment section instead of the card data for

any API request.

In this example, we will add lines to the script that will send the
opaqueData object on to another function that will insert it into the
form for submission to your server.

> function responseHandler(response) { if (response.messages.resultCode
> === \"Error\") { var i = 0;
>
> while (i \< response.messages.message.length) { console.log(
>
> response.messages.message\[i\].code + \": \" +
>
> response.messages.message\[i\].text
>
> ); i = i + 1;
>
> }
>
> } else {
>
> paymentFormUpdate(response.opaqueData);
>
> }
>
> }

## Updating and Submitting the Payment Form

Once you have successfully integrated the Accept.js library and
processed the request to tokenize the payment data, you will have
received payment nonce in the form of the dataDescriptor and dataValue ,
which you will later need for your request to the Authorize.net API. The
nonce is valid for 15 minutes.

The next step is to insert this data into your payment form so that it
can be submitted to your server. **Important: you must also remove the
card payment data from the form so that it won't get sent to your server
(as shown below).**

**Inserting the Opaque Data**

In the original sample HTML, there were two hidden input \"elds created
speci\"cally for the purpose of storing this data. It is now time to
insert the data into these \"elds.

function paymentFormUpdate(opaqueData) {

**Copy**

> document.getElementById(\"dataDescriptor\").value =
> opaqueData.dataDescri document.getElementById(\"dataValue\").value =
> opaqueData.dataValue; }

**Removing Secure Data**

> function paymentFormUpdate(opaqueData) {
>
> document.getElementById(\"dataDescriptor\").value =
> opaqueData.dataDescri document.getElementById(\"dataValue\").value =
> opaqueData.dataValue;
>
> document.getElementById(\"cardNumber\").value = \"\";
> document.getElementById(\"expMonth\").value = \"\";
> document.getElementById(\"expYear\").value = \"\";
> document.getElementById(\"cardCode\").value = \"\";
> document.getElementById(\"accountNumber\").value = \"\";
> document.getElementById(\"routingNumber\").value = \"\";
> document.getElementById(\"nameOnAccount\").value = \"\";
> document.getElementById(\"accountType\").value = \"\"; }

## Submitting the Form

This process was kicked off when the user clicked the button to submit
the payment form. Now that the process of tokenizing the card data is
complete, it is time to allow the form to submit its information to your
server. This can be done by adding a single simple JavaScript command,
as shown below.

function paymentFormUpdate(opaqueData) {

**Copy**

> document.getElementById(\"dataDescriptor\").value =
> opaqueData.dataDescri document.getElementById(\"dataValue\").value =
> opaqueData.dataValue;
>
> document.getElementById(\"cardNumber\").value = \"\";
> document.getElementById(\"expMonth\").value = \"\";
> document.getElementById(\"expYear\").value = \"\";
> document.getElementById(\"cardCode\").value = \"\";
> document.getElementById(\"accountNumber\").value = \"\";
> document.getElementById(\"routingNumber\").value = \"\";
> document.getElementById(\"nameOnAccount\").value = \"\";
> document.getElementById(\"accountType\").value = \"\";
>
> document.getElementById(\"paymentForm\").submit(); }

# Example of the Complete HTML Page

Following are examples of a complete sample HTML page, constructed from
all of the sample code snippets above:

## Using the JavaScript Library (with Your Own Payment Form)

> \<!DOCTYPE html\> \<html\>

**Copy**

> \<!\--
>
> This \"le is a standalone HTML page demonstrating usage of the
> Authorize.net Accept JavaScript library when integrated with your own
> payment form.
>
> For complete documentation for the Accept JavaScript library, see
> https://developer.authorize.net/api/reference/features/acceptjs.html
> -→
>
> \<head\>
>
> \<title\>Sample form\</title\>
>
> \</head\>
>
> \<body\>

## Using the JavaScript Library (with the Hosted Form)

> \<!DOCTYPE html\> \<html\>

**Copy**

> \<!\--
>
> This \"le is a standalone HTML page demonstrating usage of the
> Authorize.net Accept JavaScript library using the integrated payment
> information form.
>
> For complete documentation for the Accept JavaScript library, see
> https://developer.authorize.net/api/reference/features/acceptjs.html
> -→
>
> \<head\>
>
> \<title\>Sample form\</title\>
>
> \</head\>
>
> \<body\>

A complete example implementation is available in our [Sample
Application on GitHub
(https://github.com/AuthorizeNet/accept-sample-app)]{.underline}.

> **Using the Payment Nonce in an API Request**

# From Your Server

After using Accept.js, you will have a payment nonce on your server in
the form of the dataValue element, which is contained in the opaqueData
object. The nonce is valid for 15 minutes. Because this data represents
a payment card, **an**

opaqueData **payment object can be used anywhere in the Authorize.net
API that accepts a** creditCard **or** bankAccount **payment type**. The
data descriptor and data value will both be needed to build

the opaque data payment object in your transaction processing
application on your server. This opaque data object then replaces the
card or bank account information in an API request.

## Building the Opaque Data Payment Type

> opaqueData is a generic payment data type which is used to process
> tokenized

or encrypted payment information. The payment type uses a data
descriptor to identify the type of payment information and a data value
which is the tokenized or encrypted data itself.

**Opaque Data Payment Structure**

**Field Name**

**Description**

**Format**

**Req**

dataDescriptor

Identi

\"

es the type or source of

opaque payment data. For Accept

transactions, the value must be

COMMON.ACCEPT.INAPP.PAYMENT

Text

String

Yes

dataValue

The unmodi

\"

ed token or

encrypted data BLOB that

represents the payment data.

Text

String

Yes

\<payment\>

**Copy**

> \<opaqueData\>
>
> \<dataDescriptor\>COMMON.ACCEPT.INAPP.PAYMENT\</dataDescriptor\>
>
> \<dataValue\>9471056021205027705001\</dataValue\>
>
> \</opaqueData\>
>
> \</payment\>

## Sample One-Time Payment Request

Although the opaqueData can be used in numerous API methods, the most
common request is to process a one-time payment. Here is a sample
one-time payment request using our API, with an opaque data object
replacing the usual credit card object.

> \<createTransactionRequest
> xmlns=\"AnetApi/xml/v1/schema/AnetApiSchema.xs
>
> \<merchantAuthentication\>
>
> \<name\>API_LOGIN_ID\</name\>
>
> \<transactionKey\>API_TRANSACTION_KEY\</transactionKey\>
>
> \</merchantAuthentication\>
>
> \<transactionRequest\>
>
> \<transactionType\>authCaptureTransaction\</transactionType\>
>
> \<amount\>10\</amount\>
>
> \<payment\>
>
> \<opaqueData\>
>
> \<dataDescriptor\>COMMON.ACCEPT.INAPP.PAYMENT\</dataDescriptor\>
>
> \<dataValue\>9471056021205027705001\</dataValue\>
>
> \</opaqueData\>
>
> \</payment\>
>
> \</transactionRequest\>
>
> \</createTransactionRequest\>

A complete sample request and link to sample code for several
programming languages can be found in our [API Reference
Guide]{.underline}

[(https://developer.authorize.net/api/reference/#accept-suite-create-anaccept-payment-transaction)]{.underline}.

## Sample ARB Subscription Request

You can use opaqueData to create subscriptions using [Automated
Recurring Billing
(https://developer.authorize.net/api/reference/features/recurringbilling.html)]{.underline}.
Here is a sample subscription request using our API, with an opaque data
object replacing the usual credit card object.

> \<ARBCreateSubscriptionRequest
> xmlns=\"AnetApi/xml/v1/schema/AnetApiSche
>
> \<merchantAuthentication\>
>
> \<name\>3kFew5Z8Ra\</name\>
>
> \<transactionKey\>8y25f3QT9qj3A6uh\</transactionKey\>
>
> \</merchantAuthentication\>
>
> \<refId\>123456\</refId\>
>
> \<subscription\>
>
> \<name\>Sample subscription\</name\>
>
> \<paymentSchedule\>
>
> \<interval\>
>
> \<length\>1\</length\>
>
> \<unit\>months\</unit\>
>
> \</interval\>
>
> \<startDate\>2020-08-30\</startDate\>
>
> \<totalOccurrences\>12\</totalOccurrences\>
>
> \<trialOccurrences\>1\</trialOccurrences\>
>
> \</paymentSchedule\>

A complete sample request and link to sample code for several
programming languages can be found in our [API Reference
Guide]{.underline}

[(https://developer.authorize.net/api/reference/#accept-suite-create-anaccept-payment-transaction)]{.underline}

## Sample Customer Profile Request

You can use opaqueData to create [customer pro\"les]{.underline}

[(https://developer.authorize.net/api/reference/features/customerpro\"les.html)]{.underline}.
Here is a sample subscription request using our API, with an opaque data
object replacing the usual credit card object.
\<createCustomerPro\"leRequest
xmlns=\"AnetApi/xml/v1/schema/AnetApiSchem

> \<merchantAuthentication\>
>
> \<name\>4vzCzTK46uB\</name\>
>
> \<transactionKey\>4eT533qH5xD4v62S\</transactionKey\>
>
> \</merchantAuthentication\>
>
> \<pro\"le\>
>
> \<merchantCustomerId\>Merchant_Customer_ID\</merchantCustomerId\>
>
> \<description\>Pro\"le description here\</description\>
>
> \<email\>customer-pro\"le-email@here.com\</email\>
>
> \<paymentPro\"les\>
>
> \<customerType\>individual\</customerType\>
>
> \<payment\>
>
> \<opaqueData\>
>
> \<dataDescriptor\>COMMON.ACCEPT.INAPP.PAYMENT\</dataDescriptor\>
>
> \<dataValue\>eyJjb2RlIjoiNTBfMl8wNjAwMDUyNUMxREY1NEVGNDBGQkNDND
>
> \</opaqueData\>
>
> \</payment\>

A complete sample request and link to sample code for several
programming languages can be found in our [API Reference
Guide]{.underline}

[(https://developer.authorize.net/api/reference/#accept-suite-create-anaccept-payment-transaction)]{.underline}.

# Appendix: Error Codes

**Code**

**Text**

**Notes**

I_WC_01

Successful.

The request was

processed successfully.

E_WC_01

Please include Accept.js

library from CDN.

To maintain security and

take advantage of

updates, load the

Accept.js script from

the Authorize.net

servers.

E_WC_02

A HTTPS connection is

required.

The page that embeds

Accept.js must be

served over an HTTPS

connection.

E_WC_03

Accept.js is not loaded

correctly.

Ensure that the

Accept.js library is

+-------------+------------------------------+-------------------------+
|             |                              | > loaded from the       |
|             |                              | > Authorize.net servers |
|             |                              | > and that the          |
|             |                              | > Accept.js script      |
|             |                              | > executed in the       |
|             |                              | > browser matches the   |
|             |                              | > version stored on the |
|             |                              | > server. This error    |
|             |                              | > typically occurs when |
|             |                              | > the script is loaded  |
|             |                              | > from a local source,  |
|             |                              | > or when the browser   |
|             |                              | > or proxy has          |
|             |                              | > improperly cached an  |
|             |                              | > older version of the  |
|             |                              | > script.               |
+:===========:+==============================+:========================+
| E_WC_04     | Please provide mandatory     | > When sending card     |
|             | \"eld to library.            | > information, you must |
|             |                              | > include cardNumber    |
|             |                              | > and the expiration    |
|             |                              | > date                  |
|             |                              | >                       |
|             |                              | > ( month and year ).   |
|             |                              | > When sending bank     |
|             |                              | >                       |
|             |                              | > information, you must |
|             |                              | > include               |
|             |                              | >                       |
|             |                              | > accountNumber ,       |
|             |                              | > routingNumber ,       |
|             |                              | > nameOnAccount , and   |
|             |                              | > accountType .         |
+-------------+------------------------------+-------------------------+
| E_WC_05     | Please provide valid credit  | > If card information   |
|             | card number.                 | > is provided,          |
|             |                              | > cardNumber ) must     |
|             |                              | > pass a Luhn mod 10    |
|             |                              | > check.                |
+-------------+------------------------------+-------------------------+
| E_WC_06     | Please provide valid         | > If card information   |
|             | expiration month.            | > is provided, the      |
|             |                              | > expiration month (    |
|             |                              | > month ) must be a     |
|             |                              | > maximum of two        |
|             |                              | > digits.               |
+-------------+------------------------------+-------------------------+
| > E_WC_07   | Please provide valid         | > If card information   |
|             | expiration year.             | > is provided, the      |
|             |                              | > expiration year (     |
|             |                              | > year ) must be in     |
|             |                              | > either YY or YYYY     |
+-------------+------------------------------+-------------------------+

correctly. Accept.js library is

> format.

+-------------+------------------------------+-------------------------+
|             |                              | > format.               |
+:===========:+:=============================+=========================+
| E_WC_08     | If card information is       |                         |
|             | provided, the expiration     |                         |
|             | date ( month and year ) must |                         |
|             | be in the future.            |                         |
+-------------+------------------------------+-------------------------+
| > E_WC_10   | Please provide valid         | > The value of          |
|             | apiLoginID .                 | > apiLoginID must be    |
|             |                              | > same as the ID that   |
|             |                              | > you use to            |
|             |                              | > authenticate API      |
|             |                              | > requests.             |
+-------------+------------------------------+-------------------------+
| > E_WC_13   | Invalid Fingerprint.         | > The Accept.js script  |
|             |                              | > executed in the       |
|             |                              | > browser doesn\'t      |
|             |                              | > match the version     |
|             |                              | > stored on the server. |
|             |                              | > This error typically  |
|             |                              | > occurs when the       |
|             |                              | > browser or proxy has  |
|             |                              | > improperly cached an  |
|             |                              | > older version of the  |
|             |                              | > script.               |
+-------------+------------------------------+-------------------------+
| > E_WC_14   | Accept.js encryption failed. | > A general error.      |
|             |                              | > Encrypting and        |
|             |                              | > transmitting the card |
|             |                              | > data failed, either   |
|             |                              | > within the browser or |
|             |                              | > at the                |
|             |                              | >                       |
|             |                              | > Authorize.net         |
|             |                              | > servers.              |
+-------------+------------------------------+-------------------------+
| > E_WC_15   | Please provide valid CVV.    | > The value of cardCode |
|             |                              | > must be three or four |
|             |                              | > digits.               |
+-------------+------------------------------+-------------------------+
| > E_WC_16   | Please provide valid ZIP     | > The postal code ( zip |
|             | code.                        | > ) is optional but     |
|             |                              | > should be no more     |
|             |                              | > than 20 characters if |
|             |                              | > included.             |
+-------------+------------------------------+-------------------------+
| > E_WC_17   | Please provide valid card    | > The cardholder name ( |
|             | holder name.                 | > fullName ) is         |
|             |                              | > optional but should   |
|             |                              | > be no more than 64    |
|             |                              | > characters if         |
+-------------+------------------------------+-------------------------+
|             |                              | > included.             |
+-------------+------------------------------+-------------------------+
| > E_WC_18   | Client Key is required.      | > The value for         |
|             |                              | > clientKey can be      |
|             |                              | > obtained through the  |
|             |                              | >                       |
|             |                              | > Authorize.net         |
|             |                              | > Merchant Interface,   |
|             |                              | > by navigating to      |
|             |                              | > **Account \>          |
|             |                              | > Settings**            |
|             |                              | >                       |
|             |                              | > **\> Security         |
|             |                              | > Settings \>**         |
|             |                              | >                       |
|             |                              | > **General Security**  |
|             |                              | >                       |
|             |                              | > **Settings \> Manage  |
|             |                              | > Public Client Key.**  |
+-------------+------------------------------+-------------------------+
| > E_WC_19   | An error occurred during     | > Check the value of    |
|             | processing. Please try       | > the API Login ID. The |
|             | again.                       | > value of apiLoginID   |
|             |                              | > must be same as the   |
|             |                              | > ID that you use to    |
|             |                              | > authenticate API      |
|             |                              | > requests.             |
|             |                              | > Additionally, an API  |
|             |                              | > Login ID generated    |
|             |                              | > for the Sandbox       |
|             |                              | > environment can only  |
|             |                              | > be used with the      |
|             |                              | > Sandbox version of    |
|             |                              | > the script. An API    |
|             |                              | > Login ID generated    |
|             |                              | > for the Production    |
|             |                              | > environment can only  |
|             |                              | > be used with the      |
|             |                              | > Production version of |
|             |                              | > the script.           |
+-------------+------------------------------+-------------------------+
| > E_WC_21   | User authentication failed   | > Either the API Login  |
|             | due to invalid               | > ID or Public Client   |
|             | authentication values.       | > Key, or both values,  |
|             |                              | > are incorrect. The    |
|             |                              | > value of apiLoginID   |
|             |                              | > must be               |
|             |                              | >                       |
|             |                              | > same as the ID that   |
|             |                              | > you use to            |
|             |                              | > authenticate API      |
|             |                              | > requests. The value   |
|             |                              | > for clientKey can be  |
|             |                              | >                       |
|             |                              | > obtained through the  |
|             |                              | > Authorize.net         |
|             |                              | > Merchant Interface,   |
|             |                              | > by navigating to      |
|             |                              | > **Account \>          |
|             |                              | > Settings**            |
+-------------+------------------------------+-------------------------+

+-------------+------------------------------+-------------------------+
|             |                              | > **General Security**  |
|             |                              | >                       |
|             |                              | > **Settings \> Manage  |
|             |                              | > Public Client Key.**  |
+=============+==============================+:========================+
| > E_WC_23   | Please provide either card   | > Either cardData or    |
|             | information or bank          | > bankData must be      |
|             | information.                 | > provided, but not     |
|             |                              | > both.                 |
+-------------+------------------------------+-------------------------+
| > E_WC_24   | Please provide valid account | > If bank information   |
|             | number.                      | > is                    |
|             |                              | >                       |
|             |                              | > provided,             |
|             |                              | >                       |
|             |                              | > accountNumber must    |
|             |                              | >                       |
|             |                              | > be no more than 17    |
|             |                              | > digits.               |
+-------------+------------------------------+-------------------------+
| > E_WC_25   | Please provide valid routing | > If bank information   |
|             | number.                      | > is                    |
|             |                              | >                       |
|             |                              | > provided,             |
|             |                              | > routingNumber must be |
|             |                              | > nine digits.          |
+-------------+------------------------------+-------------------------+
| E_WC_26     | Please provide valid account | > If bank information   |
|             | holder name.                 | > is provided,          |
|             |                              | >                       |
|             |                              | > nameOnAccount must    |
|             |                              | >                       |
|             |                              | > be no more than 22    |
|             |                              | > characters.           |
+-------------+------------------------------+-------------------------+
| > E_WC_27   | Please provide valid account | > If bank information   |
|             | type.                        | > is provided,          |
|             |                              | > accountType must be   |
|             |                              | > one of the following: |
|             |                              | > checking , savings ,  |
|             |                              | > or businessChecking . |
+-------------+------------------------------+-------------------------+

## \> Security Settings \> \> Security Settings \>

**Visa (https://usa.visa.com/legal/privacy-policy.html)**

**Cybersource.com**(/)
**(//policy.cookiereports.com/40b0dfe0-en-gb.html)**

**Privacy (//policy.cookiereports.com/40b0dfe0-en-gb.html)**

> **Ad prefernces (//policy.cookiereports.com/40b0dfe0-en-gb.html)**
>
> **Cookie policy (//policy.cookiereports.com/40b0dfe0-en-gb.html)**

**Terms and conditions (https://www.authorize.net/about-us/terms.html)**

> © 2019-2022. Authorize.net. All rights reserved. All brand names and
> logos are the property of their respective owners, are used for
> identi\"cation purposes only, and do not imply product endorsement or
> af\"liation with Authorize.net.
