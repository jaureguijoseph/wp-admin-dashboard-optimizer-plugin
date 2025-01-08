\*\*\[PLAID AUTHORIZE / TRANSFERS\\

\\

\]{.underline}\*\*

\[Account

Linking\](https://plaid.com/docs/transfer/creating-transfers/#account-linking)

Before initiating a transfer through Plaid, your end users need to link

a bank account to your app

using \[\[Link\]{.underline}\](https://plaid.com/docs/link/), Plaid\\\'s

client-side widget. Link will connect the user\\\'s account and obtain
and

verify the account and routing number required to initiate a transfer.

See the \[\[Link

documentation\]{.underline}\](https://plaid.com/docs/link/) for more

details on setting up a Plaid Link session. At a high level, the steps

are:

1\.
Call \[\*\*\[/link/token/create\]{.underline}\*\*\](https://plaid.com/docs/api/link/#linktokencreate),

specifying transfer in the products parameter.

2\. Initialize a Link instance using the link_token created in the

previous step. For more details for your specific platform, see

the \[\[Link

documentation\]{.underline}\](https://plaid.com/docs/link/). The user

will now go through the Link flow.

3\. The onSuccess callback will indicate the user has completed the Link

flow.

Call \[\*\*\[/item/public_token/exchange\]{.underline}\*\*\](https://plaid.com/docs/api/items/#itempublic_tokenexchange) to

exchange the public_token returned by onSuccess for an access_token.

You will also need to obtain the account_id of the account you wish

to transfer funds to or from; this can also be obtained from

the metadata.accounts field in the onSuccess callback, or by

calling \[\*\*\[/accounts/get\]{.underline}\*\*\](https://plaid.com/docs/api/accounts/#accountsget).

Once a Plaid Item is created through Link, you can then immediately

process a transfer utilizing that account or initiate the transfer at a

later time.

Several major financial institutions require OAuth connections. Make

sure to complete the OAuth security questionnaire at least three weeks

ahead of time to ensure your connections are enabled by launch. For more

information, see the \[\[OAuth

Guide\]{.underline}\](https://plaid.com/docs/link/oauth/).

\[\*\*Optimizing the Link UI for

Transfer\*\*\](https://plaid.com/docs/transfer/creating-transfers/#optimizing-the-link-ui-for-transfer)

The following Link configuration options are commonly used with

Transfer:

\- \[\*\*\[Account

select\]{.underline}\*\*\](https://plaid.com/docs/link/customization/#account-select):

The \\\"Account Select: Enabled for one account\\\" setting configures

the Link UI so that the end user may only select a single account.

If you are not using this setting, you will need to build your own

UI to let your end user select which linked account they want to use

with Transfer. When using \[\[Transfer

UI\]{.underline}\](https://plaid.com/docs/transfer/using-transfer-ui/),

this setting is mandatory.

\- \[\*\*\[Embedded Institution

Search\]{.underline}\*\*\](https://plaid.com/docs/link/embedded-institution-search/):

This presentation mode shows the Link institution search screen

embedded directly into your UI, before the end user has interacted

with Link. Embedded Institution Search increases end user uptake of

pay-by-bank payment methods and is strongly recommended when

implementing Transfer as part of a pay-by-bank use case where

multiple different payment methods are supported.

\[\*\*Importing account and routing

numbers\*\*\](https://plaid.com/docs/transfer/creating-transfers/#importing-account-and-routing-numbers)

If you are migrating from another payment processor and would like to

import known account and routing numbers into Plaid, planning to

implement a custom account linking UI, or intend to use wire transfers

as a payment rail, contact your account manager about using

the \[\*\*\[/transfer/migrate_account\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfermigrate_account) endpoint.

This endpoint will return an access_token and account_id for a given

pair of account and routing numbers. Items created in this way will

always have an authorization decision rationale

of MIGRATED_ACCOUNT_ITEM, since Plaid will be unable to assess transfer

risk for these Items.

\[\*\*Expanding institution

coverage\*\*\](https://plaid.com/docs/transfer/creating-transfers/#expanding-institution-coverage)

To see if an institution supports Transfer, use the institution status

page in the Dashboard or

the \[\*\*\[/institutions/get\]{.underline}\*\*\](https://plaid.com/docs/api/institutions/#institutionsget) endpoint.

If an institution is listed as supporting Auth, it will support

Transfer.

Transfer supports all of the same flows as Auth, including the optional

micro-deposit and database-based flows, which allow you to increase the

number of supported institutions and provide pathways for end users who

can\\\'t or don\\\'t want to log in to their institutions. Items created

with Same Day micro-deposits, Database Insights, or Database Match will

always have an authorization decision rationale

of MANUALLY_VERIFIED_ITEM, since Plaid will be unable to assess transfer

risk for these Items. For more details about these flows and

instructions on implementing them, see \[\[Full Auth

coverage\]{.underline}\](https://plaid.com/docs/auth/coverage/).

\[Authorizing a

transfer\](https://plaid.com/docs/transfer/creating-transfers/#authorizing-a-transfer)

Before creating a transfer, transfers must pass a risk check and be

authorized by Plaid\\\'s authorization engine. By default, the engine
will

run compliance checks including regulatory and program factors such as

rate limits, prohibited accounts, suspicious behavior, etc. These will

only decline a small portion (\\\<1%) of account and routing numbers.
For

debit ACH, the account balance is also checked to ensure there are

sufficient funds to complete the transfer.

To use Plaid\\\'s authorization engine,

call \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate).

You will be required to specify the access_token and account_id from the

account linking step, as well as a user.legal_name, the

transaction amount, the type of the transaction (debitor credit), and

the transaction network (ach, same-day-ach, rtp, or wire \\\-- to

request wire transfers, contact your Account Manager). For ACH

transfers, an ach_class is also required. An idempotency_key is also

strongly recommended to avoid creating duplicate transfers (or being

billed for multiple authorizations). If you are a Platform Payments

(beta) customer, you will also include an originator_client_id. For more

details on these parameters,

see \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate) in

the API Reference.

Failure to provide an idempotency_key when

calling \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate) may

result in duplicate transfers when retrying requests. It is only safe to

omit idempotency keys if you have already built and thoroughly tested

your own logic to avoid creating or processing duplicate authorizations.

Even if you have, idempotency keys are still strongly recommended.

Plaid will

return \\\'approved\\\', \\\'declined\\\' or \\\'user_action_required\\\' as
the

authorization decision along with a decision_rationale and

the authorization_id. If the transaction is approved, you can proceed

to \[\[Initiate the

transfer\]{.underline}\](https://plaid.com/docs/transfer/creating-transfers/#initiating-a-transfer).

Approved authorizations are valid for 1 hour by default, unless

otherwise configured by Plaid support. You may cancel approved

authorizations through

the \[\*\*\[/transfer/authorization/cancel\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcancel) endpoint

if you no longer intend to use the authorization. Denied authorizations

will have a code and description in the decision_rationale object that

provide additional insight.

To avoid blocking

transfers, \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate) may

authorize transfers as approved in circumstances where Plaid can\\\'t

accurately predict the risk of return. Always monitor

the decision_rationale to understand the full risk of a transfer before

proceeding to the submission step.

See the table below to understand different scenarios of authorization

decisions.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\*\*SCENARIO\*\* \*\*AUTHORIZATION \*\*DECISION RATIONALE CODE\*\*

DECISION\*\*

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

Plaid performed a balance check approved null

and determined there are

sufficient funds for the

transaction amount

Plaid couldn\\\'t verify the account approved
\\\"MANUALLY_VERIFIED_ITEM\\\"

balance because the item is

created through same-day

micro-deposits

Plaid couldn\\\'t verify the account approved
\\\"MIGRATED_ACCOUNT_ITEM\\\"

balance because the item is

created through the migrate

account endpoint

Plaid couldn\\\'t verify the account approved \\\"ERROR\\\"

balance due to an error

Plaid couldn\\\'t verify the account user_action_required
\\\"ITEM_LOGIN_REQUIRED\\\"

balance because the item went into

a stale state

User-account doesn\\\'t have declined \\\"NSF\\\"

sufficient balance to complete the

debit transfer

Plaid determined the transfer is declined \\\"RISK\\\"

high-risk

Transfer limit(s) such as daily or declined
\\\"TRANSFER_LIMIT_REACHED\\\"

monthly transaction limits are

being exceeded with this transfer

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\[\*\*Handling user_action_required\*\*\](https://plaid.com/docs/transfer/creating-transfers/#handling-user_action_required)

Sometimes Plaid needs to collect additional user input in order to

properly assess transfer risk. The most common scenario is to fix a

stale Item. In this case, Plaid returns user_action_required as the

authorization decision. To complete the required user action:

1\. Create a new link token

via \[\*\*\[/link/token/create\]{.underline}\*\*\](https://plaid.com/docs/api/link/#linktokencreate).

Instead of providing access_token, you should

set \[\*\*\[transfer.authorization_id\]{.underline}\*\*\](https://plaid.com/docs/api/link/#link-token-create-request-transfer-authorization-id) in

the request.

2\. Initialize Link with the link_token. Link will automatically guide

the user through the necessary steps.

3\. You do not need to repeat the token exchange step in

Link\\\'s onSuccess callback as the Item\\\'s access_token remains

unchanged.

After completing the required user action, you can retry

the \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate) endpoint

with the same request body. You can even reuse the

same idempotency_key as idempotency does not apply

to user_action_required authorizations.

\[\*\*ACH

Guarantee\*\*\](https://plaid.com/docs/transfer/creating-transfers/#ach-guarantee)

For customers in select industries, Plaid offers an ACH Guarantee that

shields you from bank-initiated ACH returns. If your transfers are

guaranteed with Plaid, an approved decision in the authorization process

means that the transfer will have the Guarantee applied. To learn more

about Plaid\\\'s ACH Guarantee and whether you are eligible for this

service, \[\[contact
sales\]{.underline}\](https://plaid.com/contact/) or

your Plaid account manager.

\[Initiating a

transfer\](https://plaid.com/docs/transfer/creating-transfers/#initiating-a-transfer)

After assessing a transfer\\\'s risk using the authorization engine and

receiving an approved response, you can proceed to submit the transfer

for processing.

Pass the authorization_id, access_token, account_id, and

a description (a string that will be visible to the user) to

the \[\*\*\[/transfer/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate) endpoint.

To make a transfer for less than the amount authorized, provide an

optional amount; otherwise the transfer will be made for the full

authorized amount. The authorization_id will also function similarly to

an idempotency key; attempting to re-use an authorization_id will not

create a new transfer, but will return details about the already created

transfer. You can also provide the optional field metadata to include

internal reference numbers or other information to help you reconcile

the transfer.

\[\*\*\[/transfer/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate) will

return transfer_id as well as the transfer_status. All transfers begin

in a pending status. This status changes as the transfer moves through

the payment network. Whenever the status is updated, a Transfer event

will be triggered. To learn more about tracking the status of the

transfer, see \[\[event

monitoring\]{.underline}\](https://plaid.com/docs/transfer/reconciling-transfers/#event-monitoring).

\[\*\*Handling errors in transfer

creation\*\*\](https://plaid.com/docs/transfer/creating-transfers/#handling-errors-in-transfer-creation)

If you receive a retryable error code such as a 500 (Internal Server

Error) or 429 (Too Many Requests) when creating a transfer, you should

retry the transfer; Plaid uses the transfer_id as an idempotency key, so

you can be guaranteed that retries

to \[\*\*\[/transfer/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate) will

not result in duplicate transfers.

A request

to \[\*\*\[/transfer/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate) may

fail with a 500 error even if the transfer is successfully created. You

should not assume that a 500 error response to

a \[\*\*\[/transfer/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate) call

means that the transfer failed. As a source of truth for the transfer

status, use \[\[Transfer

events\]{.underline}\](https://plaid.com/docs/transfer/reconciling-transfers/).

\[\*\*Initiating an Instant Payout via RTP or

FedNow\*\*\](https://plaid.com/docs/transfer/creating-transfers/#initiating-an-instant-payout-via-rtp-or-fednow)

\*\*Find out more about using Transfer for Instant Payouts in this

3-minute overview\*\*

Initiating an Instant Payout transfer via RTP or FedNow works the same

as initiating an ACH transfer. When initiating an Instant Payout

transfer, specify network=rtp when

calling \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate). rtp as

the network refers to all real time payment rails; Plaid will

automatically route between Real Time Payment rail by TCH or FedNow

rails as necessary.

Roughly \\\~70% of accounts in the United States can receive Instant

Payouts. If the account is not eligible to receive an Instant

Payout, \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate)will

return an INVALID_FIELD error code with an error message that the

account is ineligible for Instant Payouts. If you\\\'d like to see if
the

account is eligible for Instant Payouts before

calling \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate),

use

the \[\*\*\[/transfer/capabilities/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfercapabilitiesget) endpoint.

Only credit style transfers (where you are sending funds to a user) can

be sent using Instant Payout transfers.

\[ACH processing

windows\](https://plaid.com/docs/transfer/creating-transfers/#ach-processing-windows)

Transfers that are submitted via

the \[\*\*\[/transfer/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate) endpoint

will be submitted in the next processing window. For a same-day

transfer, the cutoff time for the last window of the day is 3:30PM

Eastern Time. For a next-day transfer, the cutoff time is 5:30PM Eastern

Time. It is recommended to submit a transfer at least 15 minutes before

the cutoff time to guarantee that it will be processed before the

cutoff.

Note that same-day transfers submitted after 3:30PM Eastern Time and

before 5:30PM Eastern Time will be automatically updated to be next-day

and submitted to the network in that following ACH processing window.

This update applies to sweeps as well. This process minimizes the risk

of return due to insufficient funds by reducing the delay between

Plaid\\\'s balance check and the submission of the transfer to the ACH

network. The settlement time remains the same as it would have been if

the transfer had been submitted in next same-day window.

ACH processing windows are active only on banking days. ACH transfers

will not be submitted to the network on weekends or bank holidays. Keep

this in mind when creating transfers over weekends or on bank holidays.

To mitigate, wait until the next banking day and then authorize and

create the transfer.

See the \[\[flow of

funds\]{.underline}\](https://plaid.com/docs/transfer/flow-of-funds/) overview

for more details on how, and when, funds move.

\[Bank statement

formatting\](https://plaid.com/docs/transfer/creating-transfers/#bank-statement-formatting)

Each bank has discretion in how they format an ACH, RTP, or FedNow

transaction in their bank statements. The most common pattern used

is \\\[Company Name\\\] \\\[Phone Number\\\] \\\[Transfer
Description\\\].

\- \\\[Company Name\\\] is the name provided in your Transfer
application.

This must match a legally registered name for your company.

\- \\\[Phone Number\\\] is the phone number that you provided in your

Transfer application.

\- \\\[Transfer Description\\\] is the string that you passed into the

description field of

your \[\*\*\[/transfer/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate) request.

To request a change to your phone number or company name,
please \[\[file

a support

ticket\]{.underline}\](https://dashboard.plaid.com/support/new/product-and-development/product-troubleshooting/product-functionality).

\[Cancelling

transfers\](https://plaid.com/docs/transfer/creating-transfers/#cancelling-transfers)

To cancel a previously created transfer, call

the \[\*\*\[/transfer/cancel\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercancel) endpoint

with the appropriate transfer_id. Note that once Plaid has sent the

transfer to the payment network, it cannot be cancelled. Practically

speaking, this means that Instant Payouts via RTP/FedNow cannot be

cancelled, as these transfers are immediately sent to the payment

network. You can check use the cancellable property found

via \[\*\*\[/transfer/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transferget) to

determine if a transfer is eligible for cancellation.

\[Sweeping funds to funding

accounts\](https://plaid.com/docs/transfer/creating-transfers/#sweeping-funds-to-funding-accounts)

There are two types of bank accounts in the Plaid Transfer system.

The first is a consumer checking and savings account connected via Plaid

Link, where you are pulling money from or issuing a payout to. In the

Transfer API, this account is represented by

an access_token and account_id of a Plaid Item. A \\\"transfer\\\" is an

intent to move money to or from this account.

The second type of account involved is your own business checking

account, which is linked to your Plaid Ledger. This account is

configured with Plaid Transfer during your onboarding by using the

details provided in your application. A \\\"sweep\\\" pushes money into,
or

pulls money from, a business checking account. For example, funding your

Plaid Ledger account by

calling \[\*\*\[/transfer/ledger/deposit\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/ledger/#transferledgerdeposit) will

trigger a sweep event.

Sweeps can be observed via

the \[\*\*\[/transfer/sweep/list\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfersweeplist) and \[\*\*\[/transfer/sweep/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfersweepget)endpoints.

If you are using the \[\[legacy funding

flows\]{.underline}\](https://plaid.com/docs/transfer/legacy-flow-of-funds/) method

instead of Plaid Ledger, a transfer will automatically kick off the

appropriate sweep to complete the flow of funds. For example, if a

number of transfers are initiated to pull money from consumer accounts,

a single sweep will then be generated to push the aggregate funds. For

more details, see the \[\[legacy flow of

funds\]{.underline}\](https://plaid.com/docs/transfer/legacy-flow-of-funds/).

\[Transfer

limits\](https://plaid.com/docs/transfer/creating-transfers/#transfer-limits)

When you sign up with Plaid Transfer, you will be assigned transfer

limits, which are placed on your authorization usage. These limits are

initially assigned based on the volume expectations you provide in your

transfer application. When you successfully create an authorization

using \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate),

the amount authorized will be counted against your limits. Authorized

amounts that aren\'t used will stop counting towards your limits if the

authorization expires or is canceled

(via \[\*\*\[/transfer/authorization/cancel\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcancel)).

Any authorization you attempt to create over your limits will be

automatically declined.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\*\*LIMIT\*\* \*\*DEBIT\*\* \*\*CREDIT\*\*

\-\-\-\-\-\-\-\-\-\--
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

Single The maximum amount of a single The maximum amount of a single

Transfer debit transfer authorization credit transfer authorization

Daily The maximum amount of debit The maximum amount of credit

transfers you may authorize in transfers you may authorize in

a calendar day a calendar day

Monthly The maximum amount of debit The maximum amount of credit

transfers you may authorize in transfers you may authorize in

a calendar month a calendar month

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

Daily limits refresh every day and monthly limits refresh on the first

day of the month at 12:00 AM EST.

\[\*\*Limit

monitoring\*\*\](https://plaid.com/docs/transfer/creating-transfers/#limit-monitoring)

You can view your current limits on the \"Account Details\" page in

the \[\[Transfer

Dashboard\]{.underline}\](https://dashboard.plaid.com/transfer). You can

monitor your usage against your daily and monthly limits via the Account

Information widget on the Overview page. You can also monitor your

authorization limits and usage through the Transfer APIs.

The \[\*\*\[/transfer/configuration/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/metrics/#transferconfigurationget) endpoint

returns your configurations for each of the transfer limits.

The \[\*\*\[/transfer/metrics/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/metrics/#transfermetricsget) endpoint

contains information about your daily and monthly authorization usage

for credit and debit transfers.

Plaid will also send you automatic email notifications when your

utilization is approaching your limits. If your daily utilization

exceeds 85% of your daily limits or your monthly utilization exceeds 80%

of your monthly limits, you will receive automated email alerts. These

alerts will be sent to your ACH, Technical, and Billing contacts. You

can configure those contacts via the \[\[Company

Profile\]{.underline}\](https://dashboard.plaid.com/settings/company/compliance?tab=companyProfile) page

on the Plaid dashboard.

Any call

to \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate) that

will cause you to exceed your limits will return the

decision \\\"declined\\\" with the decision rationale code set

to TRANSFER_LIMIT_REACHED. The rationale description identifies which

specific limit has been reached.

\[\*\*Requesting limit

changes\*\*\](https://plaid.com/docs/transfer/creating-transfers/#requesting-limit-changes)

If you need to change your limits for any reason, you can request

changes via the \[\[Account

Details\]{.underline}\](https://dashboard.plaid.com/transfer/account-details) page

in the Plaid Dashboard.

Search or Ask a Question

\[\*\*\[Plaid.com\]{.underline}\*\*\](https://plaid.com/)

\[\*\*Log in\*\*\](https://dashboard.plaid.com/signin?redirect=docs)

\[\*\*Get API Keys\*\*\](https://dashboard.plaid.com/signup)

\*\*Open nav\*\*

\*\*Initiating transfers\*\*

API reference for Transfer initiation endpoints

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\*\*INITIATING TRANSFERS\*\*

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\[\[/transfer/authorization/create\]{.underline}\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate)
Create a transfer authorization

\[\[/transfer/authorization/cancel\]{.underline}\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcancel)
Cancel a transfer authorization

\[\[/transfer/create\]{.underline}\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate)
Create a transfer

\[\[/transfer/cancel\]{.underline}\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercancel)
Cancel a transfer

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\[\*\*/transfer/authorization/create\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate)

\[Create a transfer

authorization\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#create-a-transfer-authorization)

Use

the \[\*\*\[/transfer/authorization/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcreate) endpoint

to authorize a transfer. This endpoint must be called prior to

calling \[\*\*\[/transfer/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate).

The transfer authorization will expire if not used after one hour. (You

can contact your account manager to change the default authorization

lifetime.) \\

There are four possible outcomes to calling this endpoint: 

\- If the authorization.decision in the response is declined, the

proposed transfer has failed the risk check and you cannot proceed

with the transfer. 

\- If the authorization.decision is user_action_required, additional

user input is needed, usually to fix a broken bank connection,

before Plaid can properly assess the risk. You need to launch Link

in update mode to complete the required user action. When

calling \[\*\*\[/link/token/create\]{.underline}\*\*\](https://plaid.com/docs/api/link/#linktokencreate) to

get a new Link token, instead of providing access_token in the

request, you should

set \[\*\*\[transfer.authorization_id\]{.underline}\*\*\](https://plaid.com/docs/api/link/#link-token-create-request-transfer-authorization-id)as

the authorization.id. After the Link flow is completed, you may

re-attempt the authorization.

\- If the authorization.decision is approved, and

the authorization.rationale_codeis null, the transfer has passed the

risk check and you can proceed to

call \[\*\*\[/transfer/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate).

\- If the authorization.decision is approved and

the authorization.rationale_codeis non-null, the risk check could

not be run: you may proceed with the transfer, but should perform

your own risk evaluation. For more details, see the response schema.

In Plaid\\\'s Sandbox environment the decisions will be returned as

follows:\\

\- To approve a transfer with null rationale code, make an authorization

request with an amount less than the available balance in the account.\\

\- To approve a transfer with the rationale code MANUALLY_VERIFIED_ITEM,

create an Item in Link through the \[\[Same Day Micro-deposits

flow\]{.underline}\](https://plaid.com/docs/auth/coverage/testing/#testing-same-day-micro-deposits).\\

\- To get an authorization decision of user_action_required, \[\[reset
the

login for an

Item\]{.underline}\](https://plaid.com/docs/sandbox/#item_login_required).\\

\- To decline a transfer with the rationale code NSF, the available

balance on the account must be less than the authorization amount.

See \[\[Create Sandbox test

data\]{.underline}\](https://plaid.com/docs/sandbox/user-custom/) for

details on how to customize data in Sandbox.\\

\- To decline a transfer with the rationale code RISK, the available

balance on the account must be exactly \\\$0. See \[\[Create Sandbox
test

data\]{.underline}\](https://plaid.com/docs/sandbox/user-custom/) for

details on how to customize data in Sandbox.

\*\*Request fields\*\*Collapse all

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*access_token\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-access-token)

requiredstring

The Plaid access_token for the account that will be debited or credited.

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-account-id)

requiredstring

The Plaid account_id corresponding to the end-user account that will be

debited or credited.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-funding-account-id)

string

Specify the account used to fund the transfer. Should be specified if

using legacy funding methods only. If using Plaid Ledger, leave this

field blank. Customers can find a list of funding_account_ids in the

Accounts page of your Plaid Dashboard, under the \\\"Account ID\\\"
column.

If this field is left blank and you are using legacy funding methods,

this will default to the default funding_account_id specified during

onboarding. Otherwise, Plaid Ledger will be used.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-ledger-id)

string

Specify which ledger balance used to fund the transfer. Customers can

find a list of ledger_ids in the Accounts page of your Plaid Dashboard.

If this field is left blank, this will default to id of the default

ledger balance.

\[\*\*type\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-type)

requiredstring

The type of transfer. This will be either debit or credit. A debit

indicates a transfer of money into the origination account; a credit

indicates a transfer of money out of the origination account.

Possible values: debit, credit

\[\*\*network\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-network)

requiredstring

The network or rails used for the transfer.\\

For transfers submitted as ach, the next-day cutoff is 5:30 PM Eastern

Time.\\

For transfers submitted as same-day-ach, the same-day cutoff is 3:30 PM

Eastern Time. If the transfer is submitted after this cutoff but before

the next-day cutoff, it will be sent over next-day rails and will not

incur same-day charges; this will apply to both legs of the transfer if

applicable.\\

For transfers submitted as rtp, Plaid will automatically route between

Real Time Payment rail by TCH or FedNow rails as necessary. If a

transfer is submitted as rtp and the counterparty account is not

eligible for RTP, the /transfer/authorization/create request will fail

with an INVALID_FIELD error code. To pre-check to determine whether a

counterparty account can support RTP, call /transfer/capabilities/get

before calling /transfer/authorization/create.\\

Wire transfers are currently in early availability. To request access to

wire as a payment network, contact your Account Manager. For transfers

submitted as wire, the type must be credit; wire debits are not

supported.

Possible values: ach, same-day-ach, rtp, wire

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-amount)

requiredstring

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). When calling /transfer/authorization/create,
specify

the maximum amount to authorize. When calling /transfer/create, specify

the exact amount of the transfer, up to a maximum of the amount

authorized. If this field is left blank when calling /transfer/create,

the maximum amount authorized in the authorization_id will be sent.

\[\*\*ach_class\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-ach-class)

string

Specifies the use case of the transfer. Required for transfers on an ACH

network.\\

Codes supported for credits: ccd, ppd Codes supported for debits: ccd,

tel, web\\

\\\"ccd\\\" - Corporate Credit or Debit - fund transfer between two

corporate bank accounts\\

\\\"ppd\\\" - Prearranged Payment or Deposit - the transfer is part of a

pre-existing relationship with a consumer, e.g. bill payment\\

\\\"tel\\\" - Telephone-Initiated Entry\\

\\\"web\\\" - Internet-Initiated Entry - debits from a consumer\'s
account

where their authorization is obtained over the Internet

Possible values: ccd, ppd, tel, web

\[\*\*wire_details\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-wire-details)

object

Information specific to wire transfers.

Hide object

\[\*\*message_to_beneficiary\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-wire-details-message-to-beneficiary)

string

Additional information from the wire originator to the beneficiary. Max

140 characters.

\[\*\*user\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user)

requiredobject

The legal name and other information for the account holder. The

user.legal_name field is required. Other fields are not currently used

and are present to support planned future functionality.

Hide object

\[\*\*legal_name\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user-legal-name)

requiredstring

The user\\\'s legal name. If the user is a business, provide the
business

name.

\[\*\*phone_number\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user-phone-number)

string

The user\\\'s phone number.

\[\*\*email_address\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user-email-address)

string

The user\\\'s email address.

\[\*\*address\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user-address)

object

The address associated with the account holder.

Hide object

\[\*\*street\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user-address-street)

string

The street number and name (i.e., \\\"100 Market St.\\\").

\[\*\*city\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user-address-city)

string

Ex. \\\"San Francisco\\\"

\[\*\*region\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user-address-region)

string

The state or province (e.g., \\\"CA\\\").

\[\*\*postal_code\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user-address-postal-code)

string

The postal code (e.g., \\\"94103\\\").

\[\*\*country\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user-address-country)

string

A two-letter country code (e.g., \\\"US\\\").

\[\*\*device\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-device)

object

Information about the device being used to initiate the authorization.

These fields are not currently incorporated into the risk check.

Hide object

\[\*\*ip_address\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-device-ip-address)

string

The IP address of the device being used to initiate the authorization.

\[\*\*user_agent\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-device-user-agent)

string

The user agent of the device being used to initiate the authorization.

\[\*\*iso_currency_code\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-iso-currency-code)

string

The currency of the transfer amount. The default value is \\\"USD\\\".

\[\*\*idempotency_key\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-idempotency-key)

string

A random key provided by the client, per unique authorization, which

expires after 48 hours. Maximum of 50 characters.\\

The API supports idempotency for safely retrying requests without

accidentally performing the same operation twice. For example, if a

request to create an authorization fails due to a network connection

error, you can retry the request with the same idempotency key to

guarantee that only a single authorization is created.\\

Idempotency does not apply to authorizations whose decisions are

user_action_required. Therefore you may re-attempt the authorization

after completing the required user action without changing

idempotency_key.\\

This idempotency key expires after 48 hours, after which the same key

can be reused. Failure to provide this key may result in duplicate

charges.

Max length: 50

\[\*\*user_present\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-user-present)

boolean

If the end user is initiating the specific transfer themselves via an

interactive UI, this should be true; for automatic recurring payments

where the end user is not actually initiating each individual transfer,

it should be false. This field is not currently used and is present to

support planned future functionality.

\[\*\*originator_client_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-originator-client-id)

string

The Plaid client ID that is the originator of this transfer. Only needed

if creating transfers on behalf of another client as a \[\[Platform

customer\]{.underline}\](https://plaid.com/docs/transfer/application/#originators-vs-platforms).

\[\*\*test_clock_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-request-test-clock-id)

string

Plaid\'s unique identifier for a test clock. This field may only be used

when using sandbox environment. If provided, the authorization is

created at the virtual_time on the provided test clock.

const request: TransferAuthorizationCreateRequest = {

access_token:
\\\'access-sandbox-71e02f71-0960-4a27-abd2-5631e04f2175\\\',

account_id: \\\'3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\',

type: \\\'debit\\\',

network: \\\'ach\\\',

amount: \\\'12.34\\\',

ach_class: \\\'ppd\\\',

user: {

legal_name: \\\'Anne Charleston\\\',

},

};

try {

const response = await client.transferAuthorizationCreate(request);

const authorizationId = response.data.authorization.id;

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*authorization\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization)

object

Contains the authorization decision for a proposed transfer.

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-id)

string

Plaid\'s unique identifier for a transfer authorization.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-created)

string

The datetime representing when the authorization was created, in the

format 2006-01-02T15:04:05Z.

Format: date-time

\[\*\*decision\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-decision)

string

A decision regarding the proposed transfer.\\

approved \-- The proposed transfer has received the end user\\\'s
consent

and has been approved for processing by Plaid. The decision_rationale

field is set if Plaid was unable to fetch the account information. You

may proceed with the transfer, but further review is recommended. Refer

to the code field in the decision_rationale object for details.\\

declined \-- Plaid reviewed the proposed transfer and declined

processing. Refer to the code field in the decision_rationale object for

details.\\

user_action_required \-- An action is required before Plaid can assess

the transfer risk and make a decision. The most common scenario is to

update authentication for an Item. To complete the required action,

initialize Link by setting transfer.authorization_id in the request of

/link/token/create. After Link flow is completed, you may re-attempt the

authorization request.

Possible values: approved, declined, user_action_required

\[\*\*decision_rationale\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-decision-rationale)

nullableobject

The rationale for Plaid\\\'s decision regarding a proposed transfer. It
is

always set for declined decisions, and may or may not be null for

approved decisions.

Hide object

\[\*\*code\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-decision-rationale-code)

string

A code representing the rationale for approving or declining the

proposed transfer.\\

If the rationale_code is null, the transfer passed the authorization

check.\\

Any non-null value for an approved transfer indicates that the the

authorization check could not be run and that you should perform your

own risk assessment on the transfer. The code will indicate why the

check could not be run. Possible values for an approved transfer are:\\

MANUALLY_VERIFIED_ITEM \-- Item created via a manual entry flow (i.e.

Same Day Micro-deposit, Instant Micro-deposit, Database Insights, or

Database Match), limited information available.\\

ITEM_LOGIN_REQUIRED \-- Unable to collect the account information due to

Item staleness. Can be resolved by using Link and setting

\[\*\*\[transfer.authorization_id\]{.underline}\*\*\](https://plaid.com/docs/api/link/#link-token-create-request-transfer-authorization-id)

in the request to /link/token/create.\\

MIGRATED_ACCOUNT_ITEM - Item created via /transfer/migrate_account

endpoint, limited information available.\\

ERROR \-- Unable to collect the account information due to an
unspecified

error.\\

The following codes indicate that the authorization decision was

declined:\\

NSF \-- Transaction likely to result in a return due to insufficient

funds.\\

RISK - Transaction is high-risk.\\

TRANSFER_LIMIT_REACHED - One or several transfer limits are reached,

e.g. monthly transfer limit. Check the accompanying description field to

understand which limit has been reached.

Possible values: NSF, RISK, TRANSFER_LIMIT_REACHED,

MANUALLY_VERIFIED_ITEM, ITEM_LOGIN_REQUIRED,

PAYMENT_PROFILE_LOGIN_REQUIRED, ERROR, MIGRATED_ACCOUNT_ITEM, null

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-decision-rationale-description)

string

A human-readable description of the code associated with a transfer

approval or transfer decline.

\[\*\*payment_risk\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-payment-risk)

nullableobject

This object includes the scores and risk level. This response is offered

as an add-on to /transfer/authorization/create. To request access to

these fields please contact your Plaid account manager.

Hide object

\[\*\*bank_initiated_return_score\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-payment-risk-bank-initiated-return-score)

nullableinteger

A score from 1-99 that indicates the transaction return risk: a higher

risk score suggests a higher return likelihood.\\

The score evaluates the transaction return risk because an account is

overdrawn or because an ineligible account is used and covers return

codes: \\\"R01\\\", \\\"R02\\\", \\\"R03\\\", \\\"R04\\\", \\\"R06\\\",
\\\"R08\\\", \\\"R09\\\",

\\\"R13\\\", \\\"R16\\\", \\\"R17\\\", \\\"R20\\\", \\\"R23\\\". These
returns have a

turnaround time of 2 banking days.

Minimum: 1

Maximum: 99

\[\*\*customer_initiated_return_score\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-payment-risk-customer-initiated-return-score)

nullableinteger

A score from 1-99 that indicates the transaction return risk: a higher

risk score suggests a higher return likelihood.\\

The score evaluates the transaction return risk of an unauthorized debit

and covers return codes: \\\"R05\\\", \\\"R07\\\", \\\"R10\\\",
\\\"R11\\\", \\\"R29\\\".

These returns typically have a return time frame of up to 60 calendar

days. During this period, the customer of financial institutions can

dispute a transaction as unauthorized.

Minimum: 1

Maximum: 99

\[\*\*risk_level\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-payment-risk-risk-level)

nullablestring

Comprises five risk categories (high risk, medium-high risk, medium

risk, medium-low risk, low risk) based on the probability of return

Possible values: HIGH_RISK, MEDIUM_HIGH_RISK, MEDIUM_RISK,

MEDIUM_LOW_RISK, LOW_RISK

\[\*\*warnings\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-payment-risk-warnings)

\\\[object\\\]

If bank information was not available to be used in the Signal model,

this array contains warnings describing why bank data is missing. If you

want to receive an API error instead of Signal scores in the case of

missing bank data, file a support ticket or contact your Plaid account

manager.

Hide object

\[\*\*warning_type\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-payment-risk-warnings-warning-type)

string

A broad categorization of the warning. Safe for programmatic use.

\[\*\*warning_code\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-payment-risk-warnings-warning-code)

string

The warning code identifies a specific kind of warning that pertains to

the error causing bank data to be missing. Safe for programmatic use.

For more details on warning codes, please refer to Plaid standard error

codes documentation. If you receive the ITEM_LOGIN_REQUIRED warning, we

recommend re-authenticating your user by implementing Link\\\'s update

mode. This will guide your user to fix their credentials, allowing Plaid

to start fetching data again for future Signal requests.

\[\*\*warning_message\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-payment-risk-warnings-warning-message)

string

A developer-friendly representation of the warning type. This may change

over time and is not safe for programmatic use.

\[\*\*proposed_transfer\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer)

object

Details regarding the proposed transfer.

Hide object

\[\*\*ach_class\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-ach-class)

string

Specifies the use case of the transfer. Required for transfers on an ACH

network.\\

Codes supported for credits: ccd, ppd Codes supported for debits: ccd,

tel, web\\

\\\"ccd\\\" - Corporate Credit or Debit - fund transfer between two

corporate bank accounts\\

\\\"ppd\\\" - Prearranged Payment or Deposit - the transfer is part of a

pre-existing relationship with a consumer, e.g. bill payment\\

\\\"tel\\\" - Telephone-Initiated Entry\\

\\\"web\\\" - Internet-Initiated Entry - debits from a consumer\'s
account

where their authorization is obtained over the Internet

Possible values: ccd, ppd, tel, web

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-account-id)

string

The Plaid account_id for the account that will be debited or credited.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-funding-account-id)

nullablestring

The id of the associated funding account, available in the Plaid

Dashboard. If present, this indicates which of your business checking

accounts will be credited or debited.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*type\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-type)

string

The type of transfer. This will be either debit or credit. A debit

indicates a transfer of money into the origination account; a credit

indicates a transfer of money out of the origination account.

Possible values: debit, credit

\[\*\*user\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-user)

object

The legal name and other information for the account holder.

Hide object

\[\*\*legal_name\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-user-legal-name)

string

The user\\\'s legal name.

\[\*\*phone_number\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-user-phone-number)

nullablestring

The user\\\'s phone number.

\[\*\*email_address\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-user-email-address)

nullablestring

The user\\\'s email address.

\[\*\*address\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-user-address)

nullableobject

The address associated with the account holder.

Hide object

\[\*\*street\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-user-address-street)

nullablestring

The street number and name (i.e., \\\"100 Market St.\\\").

\[\*\*city\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-user-address-city)

nullablestring

Ex. \\\"San Francisco\\\"

\[\*\*region\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-user-address-region)

nullablestring

The state or province (e.g., \\\"CA\\\").

\[\*\*postal_code\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-user-address-postal-code)

nullablestring

The postal code (e.g., \\\"94103\\\").

\[\*\*country\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-user-address-country)

nullablestring

A two-letter country code (e.g., \\\"US\\\").

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-amount)

string

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). When calling /transfer/authorization/create,
specify

the maximum amount to authorize. When calling /transfer/create, specify

the exact amount of the transfer, up to a maximum of the amount

authorized. If this field is left blank when calling /transfer/create,

the maximum amount authorized in the authorization_id will be sent.

\[\*\*network\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-network)

string

The network or rails used for the transfer.

\[\*\*wire_details\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-wire-details)

nullableobject

Information specific to wire transfers.

Hide object

\[\*\*message_to_beneficiary\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-wire-details-message-to-beneficiary)

nullablestring

Additional information from the wire originator to the beneficiary. Max

140 characters.

\[\*\*iso_currency_code\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-iso-currency-code)

string

The currency of the transfer amount. The default value is \\\"USD\\\".

\[\*\*originator_client_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-originator-client-id)

nullablestring

The Plaid client ID that is the originator of this transfer. Only

present if created on behalf of another client as a \[\[Platform

customer\]{.underline}\](https://plaid.com/docs/transfer/application/#originators-vs-platforms).

\[\*\*credit_funds_source\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-authorization-proposed-transfer-credit-funds-source)

deprecatednullablestring

This field is now deprecated. You may ignore it for transfers created on

and after 12/01/2023.\\

Specifies the source of funds for the transfer. Only valid for credit

transfers, and defaults to sweep if not specified. This field is not

specified for debit transfers.\\

sweep - Sweep funds from your funding account prefunded_rtp_credits -

Use your prefunded RTP credit balance with Plaid prefunded_ach_credits -

Use your prefunded ACH credit balance with Plaid

Possible values: sweep, prefunded_rtp_credits, prefunded_ach_credits,

null

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-create-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"authorization\\\": {

\\\"id\\\": \\\"460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\",

\\\"created\\\": \\\"2020-08-06T17:27:15Z\\\",

\\\"decision\\\": \\\"approved\\\",

\\\"decision_rationale\\\": null,

\\\"guarantee_decision\\\": null,

\\\"guarantee_decision_rationale\\\": null,

\\\"payment_risk\\\": null,

\\\"proposed_transfer\\\": {

\\\"ach_class\\\": \\\"ppd\\\",

\\\"account_id\\\": \\\"3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\",

\\\"funding_account_id\\\":
\\\"8945fedc-e703-463d-86b1-dc0607b55460\\\",

\\\"ledger_id\\\": \\\"563db5f8-4c95-4e17-8c3e-cb988fb9cf1a\\\",

\\\"type\\\": \\\"credit\\\",

\\\"user\\\": {

\\\"legal_name\\\": \\\"Anne Charleston\\\",

\\\"phone_number\\\": \\\"510-555-0128\\\",

\\\"email_address\\\": \\\"acharleston@email.com\\\",

\\\"address\\\": {

\\\"street\\\": \\\"123 Main St.\\\",

\\\"city\\\": \\\"San Francisco\\\",

\\\"region\\\": \\\"CA\\\",

\\\"postal_code\\\": \\\"94053\\\",

\\\"country\\\": \\\"US\\\"

}

},

\\\"amount\\\": \\\"12.34\\\",

\\\"network\\\": \\\"ach\\\",

\\\"iso_currency_code\\\": \\\"USD\\\",

\\\"origination_account_id\\\": \\\"\\\",

\\\"originator_client_id\\\": null,

\\\"credit_funds_source\\\": \\\"sweep\\\"

}

},

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/authorization/cancel\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcancel)

\[Cancel a transfer

authorization\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#cancel-a-transfer-authorization)

Use

the \[\*\*\[/transfer/authorization/cancel\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transferauthorizationcancel) endpoint

to cancel a transfer authorization. A transfer authorization is eligible

for cancellation if it has not yet been used to create a transfer.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-cancel-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-cancel-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*authorization_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-cancel-request-authorization-id)

requiredstring

Plaid\'s unique identifier for a transfer authorization.

const request: TransferAuthorizationCancelRequest = {

authorization_id: \\\'123004561178933\\\',

};

try {

const response = await plaidClient.transferAuthorizationCancel(request);

const request_id = response.data.request_id;

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-authorization-cancel-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/create\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate)

\[Create a

transfer\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#create-a-transfer)

Use

the \[\*\*\[/transfer/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercreate) endpoint

to initiate a new transfer. This endpoint is retryable and idempotent;

if a transfer with the provided transfer_id has already been created, it

will return the transfer details without creating a new transfer. A

transfer may still be created if a 500 error is returned; to detect this

scenario, use \[\[Transfer

events\]{.underline}\](https://plaid.com/docs/transfer/reconciling-transfers/).

\*\*Request fields\*\*Collapse all

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*access_token\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-request-access-token)

requiredstring

The Plaid access_token for the account that will be debited or credited.

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-request-account-id)

requiredstring

The Plaid account_id corresponding to the end-user account that will be

debited or credited.

\[\*\*authorization_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-request-authorization-id)

requiredstring

Plaid\'s unique identifier for a transfer authorization. This parameter

also serves the purpose of acting as an idempotency identifier.

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-request-amount)

string

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). When calling /transfer/authorization/create,
specify

the maximum amount to authorize. When calling /transfer/create, specify

the exact amount of the transfer, up to a maximum of the amount

authorized. If this field is left blank when calling /transfer/create,

the maximum amount authorized in the authorization_id will be sent.

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-request-description)

requiredstring

The transfer description. Maximum of 15 characters. If reprocessing a

returned transfer, please note that the description field must be

\\\"Retry 1\\\" or \\\"Retry 2\\\" to indicate that it\\\'s a retry of a

previously returned transfer. You may retry a transfer up to 2 times,

within 180 days of creating the original transfer. Only transfers that

were returned with code R01 or R09 may be retried. For a full listing of

ACH return codes, see \[\[Transfer

errors\]{.underline}\](https://plaid.com/docs/errors/transfer/#ach-return-codes).

Max length: 15

\[\*\*metadata\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-request-metadata)

object

The Metadata object is a mapping of client-provided string fields to any

string value. The following limitations apply: The JSON values must be

Strings (no nested JSON objects allowed) Only ASCII characters may be

used Maximum of 50 key/value pairs Maximum key length of 40 characters

Maximum value length of 500 characters

\[\*\*test_clock_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-request-test-clock-id)

string

Plaid\'s unique identifier for a test clock. This field may only be used

when using sandbox environment. If provided, the transfer is created at

the virtual_time on the provided test_clock.

\[\*\*facilitator_fee\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-request-facilitator-fee)

string

The amount to deduct from transfer.amount and distribute to the

platform\'s Ledger balance as a facilitator fee (decimal string with two

digits of precision e.g. \\\"10.00\\\"). The remainder will go to the

end-customer\'s Ledger balance. This must be less than or equal to the

transfer.amount.

const request: TransferCreateRequest = {

access_token:
\\\'access-sandbox-71e02f71-0960-4a27-abd2-5631e04f2175\\\',

account_id: \\\'3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\',

description: \\\'payment\\\',

authorization_id: \\\'231h012308h3101z21909sw\\\',

};

try {

const response = await client.transferCreate(request);

const transfer = response.data.transfer;

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*transfer\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer)

object

Represents a transfer within the Transfers API.

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-id)

string

Plaid\'s unique identifier for a transfer.

\[\*\*authorization_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-authorization-id)

string

Plaid\'s unique identifier for a transfer authorization.

\[\*\*ach_class\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-ach-class)

string

Specifies the use case of the transfer. Required for transfers on an ACH

network.\\

Codes supported for credits: ccd, ppd Codes supported for debits: ccd,

tel, web\\

\\\"ccd\\\" - Corporate Credit or Debit - fund transfer between two

corporate bank accounts\\

\\\"ppd\\\" - Prearranged Payment or Deposit - the transfer is part of a

pre-existing relationship with a consumer, e.g. bill payment\\

\\\"tel\\\" - Telephone-Initiated Entry\\

\\\"web\\\" - Internet-Initiated Entry - debits from a consumer\'s
account

where their authorization is obtained over the Internet

Possible values: ccd, ppd, tel, web

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-account-id)

string

The Plaid account_id corresponding to the end-user account that will be

debited or credited.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-funding-account-id)

nullablestring

The id of the associated funding account, available in the Plaid

Dashboard. If present, this indicates which of your business checking

accounts will be credited or debited.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*type\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-type)

string

The type of transfer. This will be either debit or credit. A debit

indicates a transfer of money into the origination account; a credit

indicates a transfer of money out of the origination account.

Possible values: debit, credit

\[\*\*user\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-user)

object

The legal name and other information for the account holder.

Hide object

\[\*\*legal_name\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-user-legal-name)

string

The user\\\'s legal name.

\[\*\*phone_number\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-user-phone-number)

nullablestring

The user\\\'s phone number.

\[\*\*email_address\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-user-email-address)

nullablestring

The user\\\'s email address.

\[\*\*address\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-user-address)

nullableobject

The address associated with the account holder.

Hide object

\[\*\*street\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-user-address-street)

nullablestring

The street number and name (i.e., \\\"100 Market St.\\\").

\[\*\*city\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-user-address-city)

nullablestring

Ex. \\\"San Francisco\\\"

\[\*\*region\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-user-address-region)

nullablestring

The state or province (e.g., \\\"CA\\\").

\[\*\*postal_code\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-user-address-postal-code)

nullablestring

The postal code (e.g., \\\"94103\\\").

\[\*\*country\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-user-address-country)

nullablestring

A two-letter country code (e.g., \\\"US\\\").

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-amount)

string

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). When calling /transfer/authorization/create,
specify

the maximum amount to authorize. When calling /transfer/create, specify

the exact amount of the transfer, up to a maximum of the amount

authorized. If this field is left blank when calling /transfer/create,

the maximum amount authorized in the authorization_id will be sent.

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-description)

string

The description of the transfer.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-created)

string

The datetime when this transfer was created. This will be of the form

2006-01-02T15:04:05Z

Format: date-time

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-status)

string

The status of the transfer.\\

pending: A new transfer was created; it is in the pending state. posted:

The transfer has been successfully submitted to the payment network.

settled: Credits are available to be withdrawn or debits have been

deducted from the Plaid linked account. funds_available: Funds from the

transfer have been released from hold and applied to the ledger\\\'s

available balance. (Only applicable to ACH debits.) cancelled: The

transfer was cancelled by the client. failed: The transfer failed, no

funds were moved. returned: A posted transfer was returned.

Possible values: pending, posted, settled, funds_available, cancelled,

failed, returned

\[\*\*sweep_status\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-sweep-status)

nullablestring

The status of the sweep for the transfer.\\

unswept: The transfer hasn\\\'t been swept yet. swept: The transfer was

swept to the sweep account. swept_settled: Credits are available to be

withdrawn or debits have been deducted from the customer\'s business

checking account. return_swept: The transfer was returned, funds were

pulled back or pushed back to the sweep account. null: The transfer will

never be swept (e.g. if the transfer is cancelled or returned before

being swept)

Possible values: null, unswept, swept, swept_settled, return_swept

\[\*\*network\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-network)

string

The network or rails used for the transfer.\\

For transfers submitted as ach, the next-day cutoff is 5:30 PM Eastern

Time.\\

For transfers submitted as same-day-ach, the same-day cutoff is 3:30 PM

Eastern Time. If the transfer is submitted after this cutoff but before

the next-day cutoff, it will be sent over next-day rails and will not

incur same-day charges; this will apply to both legs of the transfer if

applicable.\\

For transfers submitted as rtp, Plaid will automatically route between

Real Time Payment rail by TCH or FedNow rails as necessary. If a

transfer is submitted as rtp and the counterparty account is not

eligible for RTP, the /transfer/authorization/create request will fail

with an INVALID_FIELD error code. To pre-check to determine whether a

counterparty account can support RTP, call /transfer/capabilities/get

before calling /transfer/authorization/create.\\

Wire transfers are currently in early availability. To request access to

wire as a payment network, contact your Account Manager. For transfers

submitted as wire, the type must be credit; wire debits are not

supported.

Possible values: ach, same-day-ach, rtp, wire

\[\*\*wire_details\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-wire-details)

nullableobject

Information specific to wire transfers.

Hide object

\[\*\*message_to_beneficiary\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-wire-details-message-to-beneficiary)

nullablestring

Additional information from the wire originator to the beneficiary. Max

140 characters.

\[\*\*cancellable\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-cancellable)

boolean

When true, you can still cancel this transfer.

\[\*\*failure_reason\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-failure-reason)

nullableobject

The failure reason if the event type for a transfer is \\\"failed\\\" or

\\\"returned\\\". Null value otherwise.

Hide object

\[\*\*ach_return_code\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-failure-reason-ach-return-code)

nullablestring

The ACH return code, e.g. R01. A return code will be provided if and

only if the transfer status is returned. For a full listing of ACH

return codes, see \[\[Transfer

errors\]{.underline}\](https://plaid.com/docs/errors/transfer/#ach-return-codes).

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-failure-reason-description)

string

A human-readable description of the reason for the failure or reversal.

\[\*\*metadata\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-metadata)

nullableobject

The Metadata object is a mapping of client-provided string fields to any

string value. The following limitations apply: The JSON values must be

Strings (no nested JSON objects allowed) Only ASCII characters may be

used Maximum of 50 key/value pairs Maximum key length of 40 characters

Maximum value length of 500 characters

\[\*\*iso_currency_code\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-iso-currency-code)

string

The currency of the transfer amount, e.g. \\\"USD\\\"

\[\*\*standard_return_window\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-standard-return-window)

nullablestring

The date 3 business days from settlement date indicating the following

ACH returns can no longer happen: R01, R02, R03, R29. This will be of

the form YYYY-MM-DD.

Format: date

\[\*\*unauthorized_return_window\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-unauthorized-return-window)

nullablestring

The date 61 business days from settlement date indicating the following

ACH returns can no longer happen: R05, R07, R10, R11, R51, R33, R37,

R38, R51, R52, R53. This will be of the form YYYY-MM-DD.

Format: date

\[\*\*expected_settlement_date\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-expected-settlement-date)

nullablestring

The expected date when the full amount of the transfer settles at the

consumers\' account, if the transfer is credit; or at the customer\\\'s

business checking account, if the transfer is debit. Only set for ACH

transfers and is null for non-ACH transfers. Only set for ACH transfers.

This will be of the form YYYY-MM-DD.

Format: date

\[\*\*originator_client_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-originator-client-id)

nullablestring

The Plaid client ID that is the originator of this transfer. Only

present if created on behalf of another client as a \[\[Platform

customer\]{.underline}\](https://plaid.com/docs/transfer/application/#originators-vs-platforms).

\[\*\*refunds\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds)

\\\[object\\\]

A list of refunds associated with this transfer.

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds-id)

string

Plaid\'s unique identifier for a refund.

\[\*\*transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds-transfer-id)

string

The ID of the transfer to refund.

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds-amount)

string

The amount of the refund (decimal string with two digits of precision

e.g. \\\"10.00\\\").

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds-status)

string

The status of the refund.\\

pending: A new refund was created; it is in the pending state. posted:

The refund has been successfully submitted to the payment network.

settled: Credits have been refunded to the Plaid linked account.

cancelled: The refund was cancelled by the client. failed: The refund

has failed. returned: The refund was returned.

Possible values: pending, posted, cancelled, failed, settled, returned

\[\*\*failure_reason\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds-failure-reason)

nullableobject

The failure reason if the event type for a refund is \\\"failed\\\" or

\\\"returned\\\". Null value otherwise.

Hide object

\[\*\*ach_return_code\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds-failure-reason-ach-return-code)

nullablestring

The ACH return code, e.g. R01. A return code will be provided if and

only if the refund status is returned. For a full listing of ACH return

codes, see \[\[Transfer

errors\]{.underline}\](https://plaid.com/docs/errors/transfer/#ach-return-codes).

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds-failure-reason-description)

string

A human-readable description of the reason for the failure or reversal.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds-created)

string

The datetime when this refund was created. This will be of the form

2006-01-02T15:04:05Z

Format: date-time

\[\*\*network_trace_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-refunds-network-trace-id)

nullablestring

The trace identifier for the transfer based on its network. This will

only be set after the transfer has posted.\\

For ach or same-day-ach transfers, this is the ACH trace number. For rtp

transfers, this is the Transaction Identification number. For wire

transfers, this is the IMAD (Input Message Accountability Data) number.

\[\*\*recurring_transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-recurring-transfer-id)

nullablestring

The id of the recurring transfer if this transfer belongs to a recurring

transfer.

\[\*\*expected_sweep_settlement_schedule\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-expected-sweep-settlement-schedule)

\\\[object\\\]

The expected sweep settlement schedule of this transfer, assuming this

transfer is not returned. Only applies to ACH debit transfers.

Hide object

\[\*\*sweep_settlement_date\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-expected-sweep-settlement-schedule-sweep-settlement-date)

string

The settlement date of a sweep for this transfer.

Format: date

\[\*\*swept_settled_amount\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-expected-sweep-settlement-schedule-swept-settled-amount)

string

The accumulated amount that has been swept by sweep_settlement_date.

\[\*\*credit_funds_source\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-credit-funds-source)

deprecatednullablestring

This field is now deprecated. You may ignore it for transfers created on

and after 12/01/2023.\\

Specifies the source of funds for the transfer. Only valid for credit

transfers, and defaults to sweep if not specified. This field is not

specified for debit transfers.\\

sweep - Sweep funds from your funding account prefunded_rtp_credits -

Use your prefunded RTP credit balance with Plaid prefunded_ach_credits -

Use your prefunded ACH credit balance with Plaid

Possible values: sweep, prefunded_rtp_credits, prefunded_ach_credits,

null

\[\*\*facilitator_fee\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-facilitator-fee)

string

The amount to deduct from transfer.amount and distribute to the

platform\'s Ledger balance as a facilitator fee (decimal string with two

digits of precision e.g. \\\"10.00\\\"). The remainder will go to the

end-customer\'s Ledger balance. This must be less than or equal to the

transfer.amount.

\[\*\*network_trace_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-transfer-network-trace-id)

nullablestring

The trace identifier for the transfer based on its network. This will

only be set after the transfer has posted.\\

For ach or same-day-ach transfers, this is the ACH trace number. For rtp

transfers, this is the Transaction Identification number. For wire

transfers, this is the IMAD (Input Message Accountability Data) number.

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-create-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"transfer\\\": {

\\\"id\\\": \\\"460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\",

\\\"authorization_id\\\": \\\"c9f90aa1-2949-c799-e2b6-ea05c89bb586\\\",

\\\"ach_class\\\": \\\"ppd\\\",

\\\"account_id\\\": \\\"3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\",

\\\"funding_account_id\\\":
\\\"8945fedc-e703-463d-86b1-dc0607b55460\\\",

\\\"ledger_id\\\": \\\"563db5f8-4c95-4e17-8c3e-cb988fb9cf1a\\\",

\\\"type\\\": \\\"credit\\\",

\\\"user\\\": {

\\\"legal_name\\\": \\\"Anne Charleston\\\",

\\\"phone_number\\\": \\\"510-555-0128\\\",

\\\"email_address\\\": \\\"acharleston@email.com\\\",

\\\"address\\\": {

\\\"street\\\": \\\"123 Main St.\\\",

\\\"city\\\": \\\"San Francisco\\\",

\\\"region\\\": \\\"CA\\\",

\\\"postal_code\\\": \\\"94053\\\",

\\\"country\\\": \\\"US\\\"

}

},

\\\"amount\\\": \\\"12.34\\\",

\\\"description\\\": \\\"payment\\\",

\\\"created\\\": \\\"2020-08-06T17:27:15Z\\\",

\\\"refunds\\\": \\\[\\\],

\\\"status\\\": \\\"pending\\\",

\\\"network\\\": \\\"ach\\\",

\\\"cancellable\\\": true,

\\\"guarantee_decision\\\": null,

\\\"guarantee_decision_rationale\\\": null,

\\\"failure_reason\\\": null,

\\\"metadata\\\": {

\\\"key1\\\": \\\"value1\\\",

\\\"key2\\\": \\\"value2\\\"

},

\\\"origination_account_id\\\": \\\"\\\",

\\\"iso_currency_code\\\": \\\"USD\\\",

\\\"standard_return_window\\\": \\\"2023-08-07\\\",

\\\"unauthorized_return_window\\\": \\\"2023-10-07\\\",

\\\"expected_settlement_date\\\": \\\"2023-08-04\\\",

\\\"originator_client_id\\\": \\\"569ed2f36b3a3a021713abc1\\\",

\\\"recurring_transfer_id\\\": null,

\\\"credit_funds_source\\\": \\\"sweep\\\",

\\\"facilitator_fee\\\": \\\"1.23\\\",

\\\"network_trace_id\\\": null

},

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/cancel\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercancel)

\[Cancel a

transfer\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#cancel-a-transfer)

Use

the \[\*\*\[/transfer/cancel\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfercancel) endpoint

to cancel a transfer. A transfer is eligible for cancellation if

the cancellable property returned

by \[\*\*\[/transfer/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transferget) is true.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-cancel-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-cancel-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-cancel-request-transfer-id)

requiredstring

Plaid\'s unique identifier for a transfer.

const request: TransferCancelRequest = {

transfer_id: \\\'123004561178933\\\',

};

try {

const response = await plaidClient.transferCancel(request);

const request_id = response.data.request_id;

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/initiating-transfers/#transfer-cancel-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Search or Ask a Question

\![Ask Bill!\](media/image1.png){width=\"0.3527777777777778in\"

height=\"0.3527777777777778in\"}

\[\*\*\[Plaid.com\]{.underline}\*\*\](https://plaid.com/)

\[\*\*Log in\*\*\](https://dashboard.plaid.com/signin?redirect=docs)

\[\*\*Get API Keys\*\*\](https://dashboard.plaid.com/signup)

\*\*Open nav\*\*

\*\*Transfer\*\*

API reference for Transfer account linking endpoints

\[Account

Linking\](https://plaid.com/docs/api/products/transfer/account-linking/#account-linking)

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\*\*ACCOUNT LINKING\*\*

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\[\[/transfer/capabilities/get\]{.underline}\](https://plaid.com/docs/api/products/transfer/account-linking/#transfercapabilitiesget)
Determine RTP eligibility for a Plaid Item

\[\[/transfer/intent/create\]{.underline}\](https://plaid.com/docs/api/products/transfer/account-linking/#transferintentcreate)
Create a transfer intent and invoke Transfer UI

(Transfer UI only)

\[\[/transfer/intent/get\]{.underline}\](https://plaid.com/docs/api/products/transfer/account-linking/#transferintentget)
Retrieve information about a transfer intent

(Transfer UI only)

\[\[/transfer/migrate_account\]{.underline}\](https://plaid.com/docs/api/products/transfer/account-linking/#transfermigrate_account)
Create an Item to use with Transfer from known

account and routing numbers

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\[\*\*/transfer/capabilities/get\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfercapabilitiesget)

\[Get RTP eligibility information of a

transfer\](https://plaid.com/docs/api/products/transfer/account-linking/#get-rtp-eligibility-information-of-a-transfer)

Use

the \[\*\*\[/transfer/capabilities/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfercapabilitiesget) endpoint

to determine the RTP eligibility information of an account to be used

with Transfer. This endpoint works on all Transfer-capable Items,

including those created

by \[\*\*\[/transfer/migrate_account\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfermigrate_account).

To simulate RTP eligibility in Sandbox, log in using the

username user_good and password pass_good and use the first two checking

and savings accounts in the \\\"First Platypus Bank\\\" institution
(ending

in 0000 or 1111), which will return true. Any other account will

return false.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-capabilities-get-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-capabilities-get-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*access_token\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-capabilities-get-request-access-token)

requiredstring

The Plaid access_token for the account that will be debited or credited.

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-capabilities-get-request-account-id)

requiredstring

The Plaid account_id corresponding to the end-user account that will be

debited or credited.

const request: TransferCapabilitiesGetRequest = {

access_token:
\\\'access-sandbox-71e02f71-0960-4a27-abd2-5631e04f2175\\\',

account_id: \\\'3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\',

};

try {

const response = await client.transferCapabilitiesGet(request);

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*institution_supported_networks\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-capabilities-get-response-institution-supported-networks)

object

Contains the RTP network and types supported by the linked Item\\\'s

institution.

Hide object

\[\*\*rtp\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-capabilities-get-response-institution-supported-networks-rtp)

object

Contains the supported service types in RTP

Hide object

\[\*\*credit\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-capabilities-get-response-institution-supported-networks-rtp-credit)

boolean

When true, the linked Item\\\'s institution supports RTP credit
transfer.

Default: false

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-capabilities-get-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"institution_supported_networks\\\": {

\\\"rtp\\\": {

\\\"credit\\\": true

}

},

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/intent/create\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transferintentcreate)

\[Create a transfer intent object to invoke the Transfer

UI\](https://plaid.com/docs/api/products/transfer/account-linking/#create-a-transfer-intent-object-to-invoke-the-transfer-ui)

Use

the \[\*\*\[/transfer/intent/create\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transferintentcreate) endpoint

to generate a transfer intent object and invoke the Transfer UI.

\*\*Request fields\*\*Collapse all

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-account-id)

string

The Plaid account_id corresponding to the end-user account that will be

debited or credited.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-funding-account-id)

string

Specify the account used to fund the transfer. Should be specified if

using legacy funding methods only. If using Plaid Ledger, leave this

field blank. Customers can find a list of funding_account_ids in the

Accounts page of your Plaid Dashboard, under the \\\"Account ID\\\"
column.

If this field is left blank and you are using legacy funding methods,

this will default to the default funding_account_id specified during

onboarding. Otherwise, Plaid Ledger will be used.

\[\*\*mode\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-mode)

requiredstring

The direction of the flow of transfer funds.\\

PAYMENT: Transfers funds from an end user\\\'s account to your business

account.\\

DISBURSEMENT: Transfers funds from your business account to an end

user\\\'s account.

Possible values: PAYMENT, DISBURSEMENT

\[\*\*network\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-network)

string

The network or rails used for the transfer. Defaults to same-day-ach.\\

For transfers submitted using ach, the next-day cutoff is 5:30 PM

Eastern Time.\\

For transfers submitted using same-day-ach, the same-day cutoff is 3:30

PM Eastern Time. If the transfer is submitted after this cutoff but

before the next-day cutoff, it will be sent over next-day rails and will

not incur same-day charges.\\

For transfers submitted using rtp, in the case that the account being

credited does not support RTP, the transfer will be sent over ACH as

long as an ach_class is provided in the request. If RTP isn\\\'t
supported

by the account and no ach_class is provided, the transfer will fail to

be submitted.

Possible values: ach, same-day-ach, rtp

Default: same-day-ach

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-amount)

requiredstring

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). When calling /transfer/authorization/create,
specify

the maximum amount to authorize. When calling /transfer/create, specify

the exact amount of the transfer, up to a maximum of the amount

authorized. If this field is left blank when calling /transfer/create,

the maximum amount authorized in the authorization_id will be sent.

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-description)

requiredstring

A description for the underlying transfer. Maximum of 15 characters.

Min length: 1

Max length: 15

\[\*\*ach_class\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-ach-class)

string

Specifies the use case of the transfer. Required for transfers on an ACH

network.\\

Codes supported for credits: ccd, ppd Codes supported for debits: ccd,

tel, web\\

\\\"ccd\\\" - Corporate Credit or Debit - fund transfer between two

corporate bank accounts\\

\\\"ppd\\\" - Prearranged Payment or Deposit - the transfer is part of a

pre-existing relationship with a consumer, e.g. bill payment\\

\\\"tel\\\" - Telephone-Initiated Entry\\

\\\"web\\\" - Internet-Initiated Entry - debits from a consumer\'s
account

where their authorization is obtained over the Internet

Possible values: ccd, ppd, tel, web

\[\*\*user\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-user)

requiredobject

The legal name and other information for the account holder.

Hide object

\[\*\*legal_name\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-user-legal-name)

requiredstring

The user\\\'s legal name.

\[\*\*phone_number\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-user-phone-number)

string

The user\\\'s phone number.

\[\*\*email_address\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-user-email-address)

string

The user\\\'s email address.

\[\*\*address\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-user-address)

object

The address associated with the account holder.

Hide object

\[\*\*street\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-user-address-street)

string

The street number and name (i.e., \\\"100 Market St.\\\").

\[\*\*city\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-user-address-city)

string

Ex. \\\"San Francisco\\\"

\[\*\*region\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-user-address-region)

string

The state or province (e.g., \\\"CA\\\").

\[\*\*postal_code\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-user-address-postal-code)

string

The postal code (e.g., \\\"94103\\\").

\[\*\*country\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-user-address-country)

string

A two-letter country code (e.g., \\\"US\\\").

\[\*\*metadata\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-metadata)

object

The Metadata object is a mapping of client-provided string fields to any

string value. The following limitations apply: The JSON values must be

Strings (no nested JSON objects allowed) Only ASCII characters may be

used Maximum of 50 key/value pairs Maximum key length of 40 characters

Maximum value length of 500 characters

\[\*\*iso_currency_code\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-request-iso-currency-code)

string

The currency of the transfer amount, e.g. \\\"USD\\\"

const request: TransferIntentCreateRequest = {

account_id: \\\'3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\',

mode: \\\'PAYMENT\\\'

amount: \\\'12.34\\\',

description: \\\'Desc\\\',

ach_class: \\\'ppd\\\',

origination_account_id: \\\'9853defc-e703-463d-86b1-dc0607a45359\\\',

user: {

legal_name: \\\'Anne Charleston\\\',

},

};

try {

const response = await client.transferIntentCreate(request);

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*transfer_intent\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent)

object

Represents a transfer intent within Transfer UI.

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-id)

string

Plaid\\\'s unique identifier for the transfer intent object.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-created)

string

The datetime the transfer was created. This will be of the form

2006-01-02T15:04:05Z.

Format: date-time

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-status)

string

The status of the transfer intent.\\

PENDING: The transfer intent is pending. SUCCEEDED: The transfer intent

was successfully created. FAILED: The transfer intent was unable to be

created.

Possible values: PENDING, SUCCEEDED, FAILED

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-account-id)

nullablestring

The Plaid account_id corresponding to the end-user account that will be

debited or credited. Returned only if account_id was set on intent

creation.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-funding-account-id)

string

The id of the funding account to use, available in the Plaid Dashboard.

This determines which of your business checking accounts will be

credited or debited.

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-amount)

string

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). When calling /transfer/authorization/create,
specify

the maximum amount to authorize. When calling /transfer/create, specify

the exact amount of the transfer, up to a maximum of the amount

authorized. If this field is left blank when calling /transfer/create,

the maximum amount authorized in the authorization_id will be sent.

\[\*\*mode\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-mode)

string

The direction of the flow of transfer funds.\\

PAYMENT: Transfers funds from an end user\\\'s account to your business

account.\\

DISBURSEMENT: Transfers funds from your business account to an end

user\\\'s account.

Possible values: PAYMENT, DISBURSEMENT

\[\*\*network\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-network)

string

The network or rails used for the transfer. Defaults to same-day-ach.\\

For transfers submitted using ach, the next-day cutoff is 5:30 PM

Eastern Time.\\

For transfers submitted using same-day-ach, the same-day cutoff is 3:30

PM Eastern Time. If the transfer is submitted after this cutoff but

before the next-day cutoff, it will be sent over next-day rails and will

not incur same-day charges.\\

For transfers submitted using rtp, in the case that the account being

credited does not support RTP, the transfer will be sent over ACH as

long as an ach_class is provided in the request. If RTP isn\\\'t
supported

by the account and no ach_class is provided, the transfer will fail to

be submitted.

Possible values: ach, same-day-ach, rtp

Default: same-day-ach

\[\*\*ach_class\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-ach-class)

string

Specifies the use case of the transfer. Required for transfers on an ACH

network.\\

Codes supported for credits: ccd, ppd Codes supported for debits: ccd,

tel, web\\

\\\"ccd\\\" - Corporate Credit or Debit - fund transfer between two

corporate bank accounts\\

\\\"ppd\\\" - Prearranged Payment or Deposit - the transfer is part of a

pre-existing relationship with a consumer, e.g. bill payment\\

\\\"tel\\\" - Telephone-Initiated Entry\\

\\\"web\\\" - Internet-Initiated Entry - debits from a consumer\'s
account

where their authorization is obtained over the Internet

Possible values: ccd, ppd, tel, web

\[\*\*user\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-user)

object

The legal name and other information for the account holder.

Hide object

\[\*\*legal_name\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-user-legal-name)

string

The user\\\'s legal name.

\[\*\*phone_number\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-user-phone-number)

nullablestring

The user\\\'s phone number.

\[\*\*email_address\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-user-email-address)

nullablestring

The user\\\'s email address.

\[\*\*address\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-user-address)

nullableobject

The address associated with the account holder.

Hide object

\[\*\*street\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-user-address-street)

nullablestring

The street number and name (i.e., \\\"100 Market St.\\\").

\[\*\*city\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-user-address-city)

nullablestring

Ex. \\\"San Francisco\\\"

\[\*\*region\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-user-address-region)

nullablestring

The state or province (e.g., \\\"CA\\\").

\[\*\*postal_code\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-user-address-postal-code)

nullablestring

The postal code (e.g., \\\"94103\\\").

\[\*\*country\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-user-address-country)

nullablestring

A two-letter country code (e.g., \\\"US\\\").

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-description)

string

A description for the underlying transfer. Maximum of 8 characters.

\[\*\*metadata\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-metadata)

nullableobject

The Metadata object is a mapping of client-provided string fields to any

string value. The following limitations apply: The JSON values must be

Strings (no nested JSON objects allowed) Only ASCII characters may be

used Maximum of 50 key/value pairs Maximum key length of 40 characters

Maximum value length of 500 characters

\[\*\*iso_currency_code\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-transfer-intent-iso-currency-code)

string

The currency of the transfer amount, e.g. \\\"USD\\\"

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-create-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"transfer_intent\\\": {

\\\"account_id\\\": \\\"3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\",

\\\"funding_account_id\\\":
\\\"9853defc-e703-463d-86b1-dc0607a45359\\\",

\\\"ach_class\\\": \\\"ppd\\\",

\\\"amount\\\": \\\"12.34\\\",

\\\"iso_currency_code\\\": \\\"USD\\\",

\\\"created\\\": \\\"2020-08-06T17:27:15Z\\\",

\\\"description\\\": \\\"Desc\\\",

\\\"id\\\": \\\"460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\",

\\\"metadata\\\": {

\\\"key1\\\": \\\"value1\\\",

\\\"key2\\\": \\\"value2\\\"

},

\\\"mode\\\": \\\"PAYMENT\\\",

\\\"origination_account_id\\\":
\\\"9853defc-e703-463d-86b1-dc0607a45359\\\",

\\\"status\\\": \\\"PENDING\\\",

\\\"user\\\": {

\\\"address\\\": {

\\\"street\\\": \\\"100 Market Street\\\",

\\\"city\\\": \\\"San Francisco\\\",

\\\"region\\\": \\\"CA\\\",

\\\"postal_code\\\": \\\"94103\\\",

\\\"country\\\": \\\"US\\\"

},

\\\"email_address\\\": \\\"acharleston@email.com\\\",

\\\"legal_name\\\": \\\"Anne Charleston\\\",

\\\"phone_number\\\": \\\"123-456-7890\\\"

}

},

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/intent/get\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transferintentget)

\[Retrieve more information about a transfer

intent\](https://plaid.com/docs/api/products/transfer/account-linking/#retrieve-more-information-about-a-transfer-intent)

Use

the \[\*\*\[/transfer/intent/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transferintentget) endpoint

to retrieve more information about a transfer intent.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*transfer_intent_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-request-transfer-intent-id)

requiredstring

Plaid\\\'s unique identifier for a transfer intent object.

const request: TransferIntentGetRequest = {

transfer_intent_id: \\\'460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\',

};

try {

const response = await client.transferIntentGet(request);

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*transfer_intent\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent)

object

Represents a transfer intent within Transfer UI.

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-id)

string

Plaid\\\'s unique identifier for a transfer intent object.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-created)

string

The datetime the transfer was created. This will be of the form

2006-01-02T15:04:05Z.

Format: date-time

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-status)

string

The status of the transfer intent.\\

PENDING: The transfer intent is pending. SUCCEEDED: The transfer intent

was successfully created. FAILED: The transfer intent was unable to be

created.

Possible values: PENDING, SUCCEEDED, FAILED

\[\*\*transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-transfer-id)

nullablestring

Plaid\\\'s unique identifier for the transfer created through the UI.

Returned only if the transfer was successfully created. Null value

otherwise.

\[\*\*failure_reason\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-failure-reason)

nullableobject

The reason for a failed transfer intent. Returned only if the transfer

intent status is failed. Null otherwise.

Hide object

\[\*\*error_type\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-failure-reason-error-type)

string

A broad categorization of the error.

\[\*\*error_code\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-failure-reason-error-code)

string

A code representing the reason for a failed transfer intent (i.e., an

API error or the authorization being declined).

\[\*\*error_message\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-failure-reason-error-message)

string

A human-readable description of the code associated with a failed

transfer intent.

\[\*\*authorization_decision\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-authorization-decision)

nullablestring

A decision regarding the proposed transfer.\\

APPROVED \-- The proposed transfer has received the end user\\\'s
consent

and has been approved for processing by Plaid. The decision_rationale

field is set if Plaid was unable to fetch the account information. You

may proceed with the transfer, but further review is recommended (i.e.,

use Link in update mode to re-authenticate your user when

decision_rationale.code is ITEM_LOGIN_REQUIRED). Refer to the code field

in the decision_rationale object for details.\\

DECLINED \-- Plaid reviewed the proposed transfer and declined

processing. Refer to the code field in the decision_rationale object for

details.

Possible values: APPROVED, DECLINED

\[\*\*authorization_decision_rationale\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-authorization-decision-rationale)

nullableobject

The rationale for Plaid\\\'s decision regarding a proposed transfer. It
is

always set for declined decisions, and may or may not be null for

approved decisions.

Hide object

\[\*\*code\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-authorization-decision-rationale-code)

string

A code representing the rationale for approving or declining the

proposed transfer.\\

If the rationale_code is null, the transfer passed the authorization

check.\\

Any non-null value for an approved transfer indicates that the the

authorization check could not be run and that you should perform your

own risk assessment on the transfer. The code will indicate why the

check could not be run. Possible values for an approved transfer are:\\

MANUALLY_VERIFIED_ITEM \-- Item created via a manual entry flow (i.e.

Same Day Micro-deposit, Instant Micro-deposit, Database Insights, or

Database Match), limited information available.\\

ITEM_LOGIN_REQUIRED \-- Unable to collect the account information due to

Item staleness. Can be resolved by using Link and setting

\[\*\*\[transfer.authorization_id\]{.underline}\*\*\](https://plaid.com/docs/api/link/#link-token-create-request-transfer-authorization-id)

in the request to /link/token/create.\\

MIGRATED_ACCOUNT_ITEM - Item created via /transfer/migrate_account

endpoint, limited information available.\\

ERROR \-- Unable to collect the account information due to an
unspecified

error.\\

The following codes indicate that the authorization decision was

declined:\\

NSF \-- Transaction likely to result in a return due to insufficient

funds.\\

RISK - Transaction is high-risk.\\

TRANSFER_LIMIT_REACHED - One or several transfer limits are reached,

e.g. monthly transfer limit. Check the accompanying description field to

understand which limit has been reached.

Possible values: NSF, RISK, TRANSFER_LIMIT_REACHED,

MANUALLY_VERIFIED_ITEM, ITEM_LOGIN_REQUIRED,

PAYMENT_PROFILE_LOGIN_REQUIRED, ERROR, MIGRATED_ACCOUNT_ITEM, null

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-authorization-decision-rationale-description)

string

A human-readable description of the code associated with a transfer

approval or transfer decline.

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-account-id)

nullablestring

The Plaid account_id for the account that will be debited or credited.

Returned only if account_id was set on intent creation.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-funding-account-id)

string

The id of the funding account to use, available in the Plaid Dashboard.

This determines which of your business checking accounts will be

credited or debited.

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-amount)

string

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). When calling /transfer/authorization/create,
specify

the maximum amount to authorize. When calling /transfer/create, specify

the exact amount of the transfer, up to a maximum of the amount

authorized. If this field is left blank when calling /transfer/create,

the maximum amount authorized in the authorization_id will be sent.

\[\*\*mode\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-mode)

string

The direction of the flow of transfer funds.\\

PAYMENT: Transfers funds from an end user\\\'s account to your business

account.\\

DISBURSEMENT: Transfers funds from your business account to an end

user\\\'s account.

Possible values: PAYMENT, DISBURSEMENT

\[\*\*network\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-network)

string

The network or rails used for the transfer. Defaults to same-day-ach.\\

For transfers submitted using ach, the next-day cutoff is 5:30 PM

Eastern Time.\\

For transfers submitted using same-day-ach, the same-day cutoff is 3:30

PM Eastern Time. If the transfer is submitted after this cutoff but

before the next-day cutoff, it will be sent over next-day rails and will

not incur same-day charges.\\

For transfers submitted using rtp, in the case that the account being

credited does not support RTP, the transfer will be sent over ACH as

long as an ach_class is provided in the request. If RTP isn\\\'t
supported

by the account and no ach_class is provided, the transfer will fail to

be submitted.

Possible values: ach, same-day-ach, rtp

Default: same-day-ach

\[\*\*ach_class\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-ach-class)

string

Specifies the use case of the transfer. Required for transfers on an ACH

network.\\

Codes supported for credits: ccd, ppd Codes supported for debits: ccd,

tel, web\\

\\\"ccd\\\" - Corporate Credit or Debit - fund transfer between two

corporate bank accounts\\

\\\"ppd\\\" - Prearranged Payment or Deposit - the transfer is part of a

pre-existing relationship with a consumer, e.g. bill payment\\

\\\"tel\\\" - Telephone-Initiated Entry\\

\\\"web\\\" - Internet-Initiated Entry - debits from a consumer\'s
account

where their authorization is obtained over the Internet

Possible values: ccd, ppd, tel, web

\[\*\*user\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-user)

object

The legal name and other information for the account holder.

Hide object

\[\*\*legal_name\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-user-legal-name)

string

The user\\\'s legal name.

\[\*\*phone_number\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-user-phone-number)

nullablestring

The user\\\'s phone number.

\[\*\*email_address\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-user-email-address)

nullablestring

The user\\\'s email address.

\[\*\*address\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-user-address)

nullableobject

The address associated with the account holder.

Hide object

\[\*\*street\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-user-address-street)

nullablestring

The street number and name (i.e., \\\"100 Market St.\\\").

\[\*\*city\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-user-address-city)

nullablestring

Ex. \\\"San Francisco\\\"

\[\*\*region\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-user-address-region)

nullablestring

The state or province (e.g., \\\"CA\\\").

\[\*\*postal_code\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-user-address-postal-code)

nullablestring

The postal code (e.g., \\\"94103\\\").

\[\*\*country\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-user-address-country)

nullablestring

A two-letter country code (e.g., \\\"US\\\").

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-description)

string

A description for the underlying transfer. Maximum of 8 characters.

\[\*\*metadata\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-metadata)

nullableobject

The Metadata object is a mapping of client-provided string fields to any

string value. The following limitations apply: The JSON values must be

Strings (no nested JSON objects allowed) Only ASCII characters may be

used Maximum of 50 key/value pairs Maximum key length of 40 characters

Maximum value length of 500 characters

\[\*\*iso_currency_code\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-transfer-intent-iso-currency-code)

string

The currency of the transfer amount, e.g. \\\"USD\\\"

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-intent-get-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"transfer_intent\\\": {

\\\"account_id\\\": \\\"3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\",

\\\"funding_account_id\\\":
\\\"9853defc-e703-463d-86b1-dc0607a45359\\\",

\\\"ach_class\\\": \\\"ppd\\\",

\\\"amount\\\": \\\"12.34\\\",

\\\"iso_currency_code\\\": \\\"USD\\\",

\\\"authorization_decision\\\": \\\"APPROVED\\\",

\\\"authorization_decision_rationale\\\": null,

\\\"created\\\": \\\"2019-12-09T17:27:15Z\\\",

\\\"description\\\": \\\"Desc\\\",

\\\"failure_reason\\\": null,

\\\"guarantee_decision\\\": null,

\\\"guarantee_decision_rationale\\\": null,

\\\"id\\\": \\\"460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\",

\\\"metadata\\\": {

\\\"key1\\\": \\\"value1\\\",

\\\"key2\\\": \\\"value2\\\"

},

\\\"mode\\\": \\\"DISBURSEMENT\\\",

\\\"origination_account_id\\\":
\\\"9853defc-e703-463d-86b1-dc0607a45359\\\",

\\\"status\\\": \\\"SUCCEEDED\\\",

\\\"transfer_id\\\": \\\"590ecd12-1dcc-7eae-4ad6-c28d1ec90df2\\\",

\\\"user\\\": {

\\\"address\\\": {

\\\"street\\\": \\\"123 Main St.\\\",

\\\"city\\\": \\\"San Francisco\\\",

\\\"region\\\": \\\"California\\\",

\\\"postal_code\\\": \\\"94053\\\",

\\\"country\\\": \\\"US\\\"

},

\\\"email_address\\\": \\\"acharleston@email.com\\\",

\\\"legal_name\\\": \\\"Anne Charleston\\\",

\\\"phone_number\\\": \\\"510-555-0128\\\"

}

},

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/migrate_account\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfermigrate_account)

\[Migrate account into

Transfers\](https://plaid.com/docs/api/products/transfer/account-linking/#migrate-account-into-transfers)

As an alternative to adding Items via Link, you can also use

the \[\*\*\[/transfer/migrate_account\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfermigrate_account) endpoint

to migrate known account and routing numbers to Plaid Items. This

endpoint is also required when adding an Item for use with wire

transfers; if you intend to create wire transfers on this account, you

must provide wire_routing_number. Note that Items created in this way

are not compatible with endpoints for other products, such

as \[\*\*\[/accounts/balance/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/balance/#accountsbalanceget),

and can only be used with Transfer endpoints. If you require access to

other endpoints, create the Item through Link instead. Access

to \[\*\*\[/transfer/migrate_account\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfermigrate_account) is

not enabled by default; to obtain access, contact your Plaid Account

Manager.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-migrate_account-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-migrate_account-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*account_number\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-migrate_account-request-account-number)

requiredstring

The user\\\'s account number.

\[\*\*routing_number\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-migrate_account-request-routing-number)

requiredstring

The user\\\'s routing number.

\[\*\*wire_routing_number\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-migrate_account-request-wire-routing-number)

string

The user\\\'s wire transfer routing number. This is the ABA number; for

some institutions, this may differ from the ACH number used in

routing_number. This field must be set for the created item to be

eligible for wire transfers.

\[\*\*account_type\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-migrate_account-request-account-type)

requiredstring

The type of the bank account (checking or savings).

const request: TransferMigrateAccountRequest = {

account_number: \\\'100000000\\\',

routing_number: \\\'121122676\\\',

account_type: \\\'checking\\\',

};

try {

const response = await plaidClient.transferMigrateAccount(request);

const access_token = response.data.access_token;

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

\[\*\*access_token\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-migrate_account-response-access-token)

string

The Plaid access_token for the newly created Item.

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-migrate_account-response-account-id)

string

The Plaid account_id for the newly created Item.

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/account-linking/#transfer-migrate_account-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"access_token\\\":

\\\"access-sandbox-435beced-94e8-4df3-a181-1dde1cfa19f0\\\",

\\\"account_id\\\": \\\"zvyDgbeeDluZ43AJP6m5fAxDlgoZXDuoy5gjN\\\",

\\\"request_id\\\": \\\"mdqfuVxeoza6mhu\\\"

}

Search or Ask a Question

\![Ask Bill!\](media/image1.png){width=\"0.3527777777777778in\"

height=\"0.3527777777777778in\"}

\[\*\*\[Plaid.com\]{.underline}\*\*\](https://plaid.com/)

\[\*\*Log in\*\*\](https://dashboard.plaid.com/signin?redirect=docs)

\[\*\*Get API Keys\*\*\](https://dashboard.plaid.com/signup)

\*\*Open nav\*\*

\*\*Reading Transfers and Transfer events\*\*

API reference for Transfer read and Transfer event endpoints and

webhooks

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\*\*READING TRANSFERS\*\*

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\[\[/transfer/get\]{.underline}\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transferget)
Retrieve information about a transfer

\[\[/transfer/list\]{.underline}\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transferlist)
Retrieve a list of transfers and their statuses

\[\[/transfer/event/list\]{.underline}\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfereventlist)
Retrieve a list of transfer events

\[\[/transfer/event/sync\]{.underline}\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfereventsync)
Sync transfer events

\[\[/transfer/sweep/get\]{.underline}\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfersweepget)
Retrieve information about a sweep

\[\[/transfer/sweep/list\]{.underline}\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfersweeplist)
Retrieve a list of sweeps

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\*\*WEBHOOKS\*\*

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\[\[TRANSFER_EVENTS_UPDATE\]{.underline}\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer_events_update)
New transfer events available

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

\[Endpoints\](https://plaid.com/docs/api/products/transfer/reading-transfers/#endpoints)

\[\*\*/transfer/get\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transferget)

\[Retrieve a

transfer\](https://plaid.com/docs/api/products/transfer/reading-transfers/#retrieve-a-transfer)

The \[\*\*\[/transfer/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transferget) endpoint

fetches information about the transfer corresponding to the

given transfer_id or authorization_id. One

of transfer_id or authorization_idmust be populated but not both.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-request-transfer-id)

string

Plaid\'s unique identifier for a transfer.

\[\*\*authorization_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-request-authorization-id)

string

Plaid\'s unique identifier for a transfer authorization.

const request: TransferGetRequest = {

transfer_id: \\\'460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\',

};

try {

const response = await plaidClient.transferGet(request);

const transfer = response.data.transfer;

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*transfer\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer)

object

Represents a transfer within the Transfers API.

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-id)

string

Plaid\'s unique identifier for a transfer.

\[\*\*authorization_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-authorization-id)

string

Plaid\'s unique identifier for a transfer authorization.

\[\*\*ach_class\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-ach-class)

string

Specifies the use case of the transfer. Required for transfers on an ACH

network.\\

Codes supported for credits: ccd, ppd Codes supported for debits: ccd,

tel, web\\

\\\"ccd\\\" - Corporate Credit or Debit - fund transfer between two

corporate bank accounts\\

\\\"ppd\\\" - Prearranged Payment or Deposit - the transfer is part of a

pre-existing relationship with a consumer, e.g. bill payment\\

\\\"tel\\\" - Telephone-Initiated Entry\\

\\\"web\\\" - Internet-Initiated Entry - debits from a consumer\'s
account

where their authorization is obtained over the Internet

Possible values: ccd, ppd, tel, web

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-account-id)

string

The Plaid account_id corresponding to the end-user account that will be

debited or credited.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-funding-account-id)

nullablestring

The id of the associated funding account, available in the Plaid

Dashboard. If present, this indicates which of your business checking

accounts will be credited or debited.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*type\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-type)

string

The type of transfer. This will be either debit or credit. A debit

indicates a transfer of money into the origination account; a credit

indicates a transfer of money out of the origination account.

Possible values: debit, credit

\[\*\*user\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-user)

object

The legal name and other information for the account holder.

Hide object

\[\*\*legal_name\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-user-legal-name)

string

The user\\\'s legal name.

\[\*\*phone_number\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-user-phone-number)

nullablestring

The user\\\'s phone number.

\[\*\*email_address\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-user-email-address)

nullablestring

The user\\\'s email address.

\[\*\*address\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-user-address)

nullableobject

The address associated with the account holder.

Hide object

\[\*\*street\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-user-address-street)

nullablestring

The street number and name (i.e., \\\"100 Market St.\\\").

\[\*\*city\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-user-address-city)

nullablestring

Ex. \\\"San Francisco\\\"

\[\*\*region\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-user-address-region)

nullablestring

The state or province (e.g., \\\"CA\\\").

\[\*\*postal_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-user-address-postal-code)

nullablestring

The postal code (e.g., \\\"94103\\\").

\[\*\*country\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-user-address-country)

nullablestring

A two-letter country code (e.g., \\\"US\\\").

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-amount)

string

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). When calling /transfer/authorization/create,
specify

the maximum amount to authorize. When calling /transfer/create, specify

the exact amount of the transfer, up to a maximum of the amount

authorized. If this field is left blank when calling /transfer/create,

the maximum amount authorized in the authorization_id will be sent.

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-description)

string

The description of the transfer.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-created)

string

The datetime when this transfer was created. This will be of the form

2006-01-02T15:04:05Z

Format: date-time

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-status)

string

The status of the transfer.\\

pending: A new transfer was created; it is in the pending state. posted:

The transfer has been successfully submitted to the payment network.

settled: Credits are available to be withdrawn or debits have been

deducted from the Plaid linked account. funds_available: Funds from the

transfer have been released from hold and applied to the ledger\\\'s

available balance. (Only applicable to ACH debits.) cancelled: The

transfer was cancelled by the client. failed: The transfer failed, no

funds were moved. returned: A posted transfer was returned.

Possible values: pending, posted, settled, funds_available, cancelled,

failed, returned

\[\*\*sweep_status\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-sweep-status)

nullablestring

The status of the sweep for the transfer.\\

unswept: The transfer hasn\\\'t been swept yet. swept: The transfer was

swept to the sweep account. swept_settled: Credits are available to be

withdrawn or debits have been deducted from the customer\'s business

checking account. return_swept: The transfer was returned, funds were

pulled back or pushed back to the sweep account. null: The transfer will

never be swept (e.g. if the transfer is cancelled or returned before

being swept)

Possible values: null, unswept, swept, swept_settled, return_swept

\[\*\*network\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-network)

string

The network or rails used for the transfer.\\

For transfers submitted as ach, the next-day cutoff is 5:30 PM Eastern

Time.\\

For transfers submitted as same-day-ach, the same-day cutoff is 3:30 PM

Eastern Time. If the transfer is submitted after this cutoff but before

the next-day cutoff, it will be sent over next-day rails and will not

incur same-day charges; this will apply to both legs of the transfer if

applicable.\\

For transfers submitted as rtp, Plaid will automatically route between

Real Time Payment rail by TCH or FedNow rails as necessary. If a

transfer is submitted as rtp and the counterparty account is not

eligible for RTP, the /transfer/authorization/create request will fail

with an INVALID_FIELD error code. To pre-check to determine whether a

counterparty account can support RTP, call /transfer/capabilities/get

before calling /transfer/authorization/create.\\

Wire transfers are currently in early availability. To request access to

wire as a payment network, contact your Account Manager. For transfers

submitted as wire, the type must be credit; wire debits are not

supported.

Possible values: ach, same-day-ach, rtp, wire

\[\*\*wire_details\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-wire-details)

nullableobject

Information specific to wire transfers.

Hide object

\[\*\*message_to_beneficiary\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-wire-details-message-to-beneficiary)

nullablestring

Additional information from the wire originator to the beneficiary. Max

140 characters.

\[\*\*cancellable\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-cancellable)

boolean

When true, you can still cancel this transfer.

\[\*\*failure_reason\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-failure-reason)

nullableobject

The failure reason if the event type for a transfer is \\\"failed\\\" or

\\\"returned\\\". Null value otherwise.

Hide object

\[\*\*ach_return_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-failure-reason-ach-return-code)

nullablestring

The ACH return code, e.g. R01. A return code will be provided if and

only if the transfer status is returned. For a full listing of ACH

return codes, see \[\[Transfer

errors\]{.underline}\](https://plaid.com/docs/errors/transfer/#ach-return-codes).

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-failure-reason-description)

string

A human-readable description of the reason for the failure or reversal.

\[\*\*metadata\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-metadata)

nullableobject

The Metadata object is a mapping of client-provided string fields to any

string value. The following limitations apply: The JSON values must be

Strings (no nested JSON objects allowed) Only ASCII characters may be

used Maximum of 50 key/value pairs Maximum key length of 40 characters

Maximum value length of 500 characters

\[\*\*iso_currency_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-iso-currency-code)

string

The currency of the transfer amount, e.g. \\\"USD\\\"

\[\*\*standard_return_window\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-standard-return-window)

nullablestring

The date 3 business days from settlement date indicating the following

ACH returns can no longer happen: R01, R02, R03, R29. This will be of

the form YYYY-MM-DD.

Format: date

\[\*\*unauthorized_return_window\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-unauthorized-return-window)

nullablestring

The date 61 business days from settlement date indicating the following

ACH returns can no longer happen: R05, R07, R10, R11, R51, R33, R37,

R38, R51, R52, R53. This will be of the form YYYY-MM-DD.

Format: date

\[\*\*expected_settlement_date\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-expected-settlement-date)

nullablestring

The expected date when the full amount of the transfer settles at the

consumers\' account, if the transfer is credit; or at the customer\\\'s

business checking account, if the transfer is debit. Only set for ACH

transfers and is null for non-ACH transfers. Only set for ACH transfers.

This will be of the form YYYY-MM-DD.

Format: date

\[\*\*originator_client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-originator-client-id)

nullablestring

The Plaid client ID that is the originator of this transfer. Only

present if created on behalf of another client as a \[\[Platform

customer\]{.underline}\](https://plaid.com/docs/transfer/application/#originators-vs-platforms).

\[\*\*refunds\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds)

\\\[object\\\]

A list of refunds associated with this transfer.

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds-id)

string

Plaid\'s unique identifier for a refund.

\[\*\*transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds-transfer-id)

string

The ID of the transfer to refund.

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds-amount)

string

The amount of the refund (decimal string with two digits of precision

e.g. \\\"10.00\\\").

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds-status)

string

The status of the refund.\\

pending: A new refund was created; it is in the pending state. posted:

The refund has been successfully submitted to the payment network.

settled: Credits have been refunded to the Plaid linked account.

cancelled: The refund was cancelled by the client. failed: The refund

has failed. returned: The refund was returned.

Possible values: pending, posted, cancelled, failed, settled, returned

\[\*\*failure_reason\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds-failure-reason)

nullableobject

The failure reason if the event type for a refund is \\\"failed\\\" or

\\\"returned\\\". Null value otherwise.

Hide object

\[\*\*ach_return_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds-failure-reason-ach-return-code)

nullablestring

The ACH return code, e.g. R01. A return code will be provided if and

only if the refund status is returned. For a full listing of ACH return

codes, see \[\[Transfer

errors\]{.underline}\](https://plaid.com/docs/errors/transfer/#ach-return-codes).

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds-failure-reason-description)

string

A human-readable description of the reason for the failure or reversal.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds-created)

string

The datetime when this refund was created. This will be of the form

2006-01-02T15:04:05Z

Format: date-time

\[\*\*network_trace_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-refunds-network-trace-id)

nullablestring

The trace identifier for the transfer based on its network. This will

only be set after the transfer has posted.\\

For ach or same-day-ach transfers, this is the ACH trace number. For rtp

transfers, this is the Transaction Identification number. For wire

transfers, this is the IMAD (Input Message Accountability Data) number.

\[\*\*recurring_transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-recurring-transfer-id)

nullablestring

The id of the recurring transfer if this transfer belongs to a recurring

transfer.

\[\*\*expected_sweep_settlement_schedule\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-expected-sweep-settlement-schedule)

\\\[object\\\]

The expected sweep settlement schedule of this transfer, assuming this

transfer is not returned. Only applies to ACH debit transfers.

Hide object

\[\*\*sweep_settlement_date\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-expected-sweep-settlement-schedule-sweep-settlement-date)

string

The settlement date of a sweep for this transfer.

Format: date

\[\*\*swept_settled_amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-expected-sweep-settlement-schedule-swept-settled-amount)

string

The accumulated amount that has been swept by sweep_settlement_date.

\[\*\*credit_funds_source\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-credit-funds-source)

deprecatednullablestring

This field is now deprecated. You may ignore it for transfers created on

and after 12/01/2023.\\

Specifies the source of funds for the transfer. Only valid for credit

transfers, and defaults to sweep if not specified. This field is not

specified for debit transfers.\\

sweep - Sweep funds from your funding account prefunded_rtp_credits -

Use your prefunded RTP credit balance with Plaid prefunded_ach_credits -

Use your prefunded ACH credit balance with Plaid

Possible values: sweep, prefunded_rtp_credits, prefunded_ach_credits,

null

\[\*\*facilitator_fee\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-facilitator-fee)

string

The amount to deduct from transfer.amount and distribute to the

platform\'s Ledger balance as a facilitator fee (decimal string with two

digits of precision e.g. \\\"10.00\\\"). The remainder will go to the

end-customer\'s Ledger balance. This must be less than or equal to the

transfer.amount.

\[\*\*network_trace_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-transfer-network-trace-id)

nullablestring

The trace identifier for the transfer based on its network. This will

only be set after the transfer has posted.\\

For ach or same-day-ach transfers, this is the ACH trace number. For rtp

transfers, this is the Transaction Identification number. For wire

transfers, this is the IMAD (Input Message Accountability Data) number.

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-get-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"transfer\\\": {

\\\"account_id\\\": \\\"3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\",

\\\"funding_account_id\\\":
\\\"8945fedc-e703-463d-86b1-dc0607b55460\\\",

\\\"ledger_id\\\": \\\"563db5f8-4c95-4e17-8c3e-cb988fb9cf1a\\\",

\\\"ach_class\\\": \\\"ppd\\\",

\\\"amount\\\": \\\"12.34\\\",

\\\"cancellable\\\": true,

\\\"created\\\": \\\"2020-08-06T17:27:15Z\\\",

\\\"description\\\": \\\"Desc\\\",

\\\"guarantee_decision\\\": null,

\\\"guarantee_decision_rationale\\\": null,

\\\"failure_reason\\\": {

\\\"ach_return_code\\\": \\\"R13\\\",

\\\"description\\\": \\\"Invalid ACH routing number\\\"

},

\\\"id\\\": \\\"460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\",

\\\"authorization_id\\\": \\\"c9f90aa1-2949-c799-e2b6-ea05c89bb586\\\",

\\\"metadata\\\": {

\\\"key1\\\": \\\"value1\\\",

\\\"key2\\\": \\\"value2\\\"

},

\\\"network\\\": \\\"ach\\\",

\\\"origination_account_id\\\": \\\"\\\",

\\\"originator_client_id\\\": null,

\\\"refunds\\\": \\\[\\\],

\\\"status\\\": \\\"pending\\\",

\\\"type\\\": \\\"credit\\\",

\\\"iso_currency_code\\\": \\\"USD\\\",

\\\"standard_return_window\\\": \\\"2020-08-07\\\",

\\\"unauthorized_return_window\\\": \\\"2020-10-07\\\",

\\\"expected_settlement_date\\\": \\\"2020-08-04\\\",

\\\"user\\\": {

\\\"email_address\\\": \\\"acharleston@email.com\\\",

\\\"legal_name\\\": \\\"Anne Charleston\\\",

\\\"phone_number\\\": \\\"510-555-0128\\\",

\\\"address\\\": {

\\\"street\\\": \\\"123 Main St.\\\",

\\\"city\\\": \\\"San Francisco\\\",

\\\"region\\\": \\\"CA\\\",

\\\"postal_code\\\": \\\"94053\\\",

\\\"country\\\": \\\"US\\\"

}

},

\\\"recurring_transfer_id\\\": null,

\\\"credit_funds_source\\\": \\\"sweep\\\",

\\\"facilitator_fee\\\": \\\"1.23\\\",

\\\"network_trace_id\\\": null

},

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/list\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transferlist)

\[List

transfers\](https://plaid.com/docs/api/products/transfer/reading-transfers/#list-transfers)

Use

the \[\*\*\[/transfer/list\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transferlist) endpoint

to see a list of all your transfers and their statuses. Results are

paginated; use the count and offset query parameters to retrieve the

desired transfers.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*start_date\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-request-start-date)

string

The start datetime of transfers to list. This should be in RFC 3339

format (i.e. 2019-12-06T22:35:49Z)

Format: date-time

\[\*\*end_date\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-request-end-date)

string

The end datetime of transfers to list. This should be in RFC 3339 format

(i.e. 2019-12-06T22:35:49Z)

Format: date-time

\[\*\*count\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-request-count)

integer

The maximum number of transfers to return.

Minimum: 1

Maximum: 25

Default: 25

\[\*\*offset\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-request-offset)

integer

The number of transfers to skip before returning results.

Default: 0

Minimum: 0

\[\*\*originator_client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-request-originator-client-id)

string

Filter transfers to only those with the specified originator client.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-request-funding-account-id)

string

Filter transfers to only those with the specified funding_account_id.

const request: TransferListRequest = {

start_date: \\\'2019-12-06T22:35:49Z\\\',

end_date: \\\'2019-12-12T22:35:49Z\\\',

count: 14,

offset: 2,

origination_account_id: \\\'8945fedc-e703-463d-86b1-dc0607b55460\\\',

};

try {

const response = await plaidClient.transferList(request);

const transfers = response.data.transfers;

for (const transfer of transfers) {

// iterate through transfers

}

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*transfers\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers)

\\\[object\\\]

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-id)

string

Plaid\'s unique identifier for a transfer.

\[\*\*authorization_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-authorization-id)

string

Plaid\'s unique identifier for a transfer authorization.

\[\*\*ach_class\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-ach-class)

string

Specifies the use case of the transfer. Required for transfers on an ACH

network.\\

Codes supported for credits: ccd, ppd Codes supported for debits: ccd,

tel, web\\

\\\"ccd\\\" - Corporate Credit or Debit - fund transfer between two

corporate bank accounts\\

\\\"ppd\\\" - Prearranged Payment or Deposit - the transfer is part of a

pre-existing relationship with a consumer, e.g. bill payment\\

\\\"tel\\\" - Telephone-Initiated Entry\\

\\\"web\\\" - Internet-Initiated Entry - debits from a consumer\'s
account

where their authorization is obtained over the Internet

Possible values: ccd, ppd, tel, web

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-account-id)

string

The Plaid account_id corresponding to the end-user account that will be

debited or credited.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-funding-account-id)

nullablestring

The id of the associated funding account, available in the Plaid

Dashboard. If present, this indicates which of your business checking

accounts will be credited or debited.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*type\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-type)

string

The type of transfer. This will be either debit or credit. A debit

indicates a transfer of money into the origination account; a credit

indicates a transfer of money out of the origination account.

Possible values: debit, credit

\[\*\*user\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-user)

object

The legal name and other information for the account holder.

Hide object

\[\*\*legal_name\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-user-legal-name)

string

The user\\\'s legal name.

\[\*\*phone_number\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-user-phone-number)

nullablestring

The user\\\'s phone number.

\[\*\*email_address\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-user-email-address)

nullablestring

The user\\\'s email address.

\[\*\*address\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-user-address)

nullableobject

The address associated with the account holder.

Hide object

\[\*\*street\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-user-address-street)

nullablestring

The street number and name (i.e., \\\"100 Market St.\\\").

\[\*\*city\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-user-address-city)

nullablestring

Ex. \\\"San Francisco\\\"

\[\*\*region\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-user-address-region)

nullablestring

The state or province (e.g., \\\"CA\\\").

\[\*\*postal_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-user-address-postal-code)

nullablestring

The postal code (e.g., \\\"94103\\\").

\[\*\*country\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-user-address-country)

nullablestring

A two-letter country code (e.g., \\\"US\\\").

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-amount)

string

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). When calling /transfer/authorization/create,
specify

the maximum amount to authorize. When calling /transfer/create, specify

the exact amount of the transfer, up to a maximum of the amount

authorized. If this field is left blank when calling /transfer/create,

the maximum amount authorized in the authorization_id will be sent.

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-description)

string

The description of the transfer.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-created)

string

The datetime when this transfer was created. This will be of the form

2006-01-02T15:04:05Z

Format: date-time

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-status)

string

The status of the transfer.\\

pending: A new transfer was created; it is in the pending state. posted:

The transfer has been successfully submitted to the payment network.

settled: Credits are available to be withdrawn or debits have been

deducted from the Plaid linked account. funds_available: Funds from the

transfer have been released from hold and applied to the ledger\\\'s

available balance. (Only applicable to ACH debits.) cancelled: The

transfer was cancelled by the client. failed: The transfer failed, no

funds were moved. returned: A posted transfer was returned.

Possible values: pending, posted, settled, funds_available, cancelled,

failed, returned

\[\*\*sweep_status\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-sweep-status)

nullablestring

The status of the sweep for the transfer.\\

unswept: The transfer hasn\\\'t been swept yet. swept: The transfer was

swept to the sweep account. swept_settled: Credits are available to be

withdrawn or debits have been deducted from the customer\'s business

checking account. return_swept: The transfer was returned, funds were

pulled back or pushed back to the sweep account. null: The transfer will

never be swept (e.g. if the transfer is cancelled or returned before

being swept)

Possible values: null, unswept, swept, swept_settled, return_swept

\[\*\*network\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-network)

string

The network or rails used for the transfer.\\

For transfers submitted as ach, the next-day cutoff is 5:30 PM Eastern

Time.\\

For transfers submitted as same-day-ach, the same-day cutoff is 3:30 PM

Eastern Time. If the transfer is submitted after this cutoff but before

the next-day cutoff, it will be sent over next-day rails and will not

incur same-day charges; this will apply to both legs of the transfer if

applicable.\\

For transfers submitted as rtp, Plaid will automatically route between

Real Time Payment rail by TCH or FedNow rails as necessary. If a

transfer is submitted as rtp and the counterparty account is not

eligible for RTP, the /transfer/authorization/create request will fail

with an INVALID_FIELD error code. To pre-check to determine whether a

counterparty account can support RTP, call /transfer/capabilities/get

before calling /transfer/authorization/create.\\

Wire transfers are currently in early availability. To request access to

wire as a payment network, contact your Account Manager. For transfers

submitted as wire, the type must be credit; wire debits are not

supported.

Possible values: ach, same-day-ach, rtp, wire

\[\*\*wire_details\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-wire-details)

nullableobject

Information specific to wire transfers.

Hide object

\[\*\*message_to_beneficiary\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-wire-details-message-to-beneficiary)

nullablestring

Additional information from the wire originator to the beneficiary. Max

140 characters.

\[\*\*cancellable\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-cancellable)

boolean

When true, you can still cancel this transfer.

\[\*\*failure_reason\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-failure-reason)

nullableobject

The failure reason if the event type for a transfer is \\\"failed\\\" or

\\\"returned\\\". Null value otherwise.

Hide object

\[\*\*ach_return_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-failure-reason-ach-return-code)

nullablestring

The ACH return code, e.g. R01. A return code will be provided if and

only if the transfer status is returned. For a full listing of ACH

return codes, see \[\[Transfer

errors\]{.underline}\](https://plaid.com/docs/errors/transfer/#ach-return-codes).

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-failure-reason-description)

string

A human-readable description of the reason for the failure or reversal.

\[\*\*metadata\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-metadata)

nullableobject

The Metadata object is a mapping of client-provided string fields to any

string value. The following limitations apply: The JSON values must be

Strings (no nested JSON objects allowed) Only ASCII characters may be

used Maximum of 50 key/value pairs Maximum key length of 40 characters

Maximum value length of 500 characters

\[\*\*iso_currency_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-iso-currency-code)

string

The currency of the transfer amount, e.g. \\\"USD\\\"

\[\*\*standard_return_window\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-standard-return-window)

nullablestring

The date 3 business days from settlement date indicating the following

ACH returns can no longer happen: R01, R02, R03, R29. This will be of

the form YYYY-MM-DD.

Format: date

\[\*\*unauthorized_return_window\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-unauthorized-return-window)

nullablestring

The date 61 business days from settlement date indicating the following

ACH returns can no longer happen: R05, R07, R10, R11, R51, R33, R37,

R38, R51, R52, R53. This will be of the form YYYY-MM-DD.

Format: date

\[\*\*expected_settlement_date\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-expected-settlement-date)

nullablestring

The expected date when the full amount of the transfer settles at the

consumers\' account, if the transfer is credit; or at the customer\\\'s

business checking account, if the transfer is debit. Only set for ACH

transfers and is null for non-ACH transfers. Only set for ACH transfers.

This will be of the form YYYY-MM-DD.

Format: date

\[\*\*originator_client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-originator-client-id)

nullablestring

The Plaid client ID that is the originator of this transfer. Only

present if created on behalf of another client as a \[\[Platform

customer\]{.underline}\](https://plaid.com/docs/transfer/application/#originators-vs-platforms).

\[\*\*refunds\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds)

\\\[object\\\]

A list of refunds associated with this transfer.

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds-id)

string

Plaid\'s unique identifier for a refund.

\[\*\*transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds-transfer-id)

string

The ID of the transfer to refund.

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds-amount)

string

The amount of the refund (decimal string with two digits of precision

e.g. \\\"10.00\\\").

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds-status)

string

The status of the refund.\\

pending: A new refund was created; it is in the pending state. posted:

The refund has been successfully submitted to the payment network.

settled: Credits have been refunded to the Plaid linked account.

cancelled: The refund was cancelled by the client. failed: The refund

has failed. returned: The refund was returned.

Possible values: pending, posted, cancelled, failed, settled, returned

\[\*\*failure_reason\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds-failure-reason)

nullableobject

The failure reason if the event type for a refund is \\\"failed\\\" or

\\\"returned\\\". Null value otherwise.

Hide object

\[\*\*ach_return_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds-failure-reason-ach-return-code)

nullablestring

The ACH return code, e.g. R01. A return code will be provided if and

only if the refund status is returned. For a full listing of ACH return

codes, see \[\[Transfer

errors\]{.underline}\](https://plaid.com/docs/errors/transfer/#ach-return-codes).

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds-failure-reason-description)

string

A human-readable description of the reason for the failure or reversal.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds-created)

string

The datetime when this refund was created. This will be of the form

2006-01-02T15:04:05Z

Format: date-time

\[\*\*network_trace_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-refunds-network-trace-id)

nullablestring

The trace identifier for the transfer based on its network. This will

only be set after the transfer has posted.\\

For ach or same-day-ach transfers, this is the ACH trace number. For rtp

transfers, this is the Transaction Identification number. For wire

transfers, this is the IMAD (Input Message Accountability Data) number.

\[\*\*recurring_transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-recurring-transfer-id)

nullablestring

The id of the recurring transfer if this transfer belongs to a recurring

transfer.

\[\*\*expected_sweep_settlement_schedule\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-expected-sweep-settlement-schedule)

\\\[object\\\]

The expected sweep settlement schedule of this transfer, assuming this

transfer is not returned. Only applies to ACH debit transfers.

Hide object

\[\*\*sweep_settlement_date\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-expected-sweep-settlement-schedule-sweep-settlement-date)

string

The settlement date of a sweep for this transfer.

Format: date

\[\*\*swept_settled_amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-expected-sweep-settlement-schedule-swept-settled-amount)

string

The accumulated amount that has been swept by sweep_settlement_date.

\[\*\*credit_funds_source\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-credit-funds-source)

deprecatednullablestring

This field is now deprecated. You may ignore it for transfers created on

and after 12/01/2023.\\

Specifies the source of funds for the transfer. Only valid for credit

transfers, and defaults to sweep if not specified. This field is not

specified for debit transfers.\\

sweep - Sweep funds from your funding account prefunded_rtp_credits -

Use your prefunded RTP credit balance with Plaid prefunded_ach_credits -

Use your prefunded ACH credit balance with Plaid

Possible values: sweep, prefunded_rtp_credits, prefunded_ach_credits,

null

\[\*\*facilitator_fee\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-facilitator-fee)

string

The amount to deduct from transfer.amount and distribute to the

platform\'s Ledger balance as a facilitator fee (decimal string with two

digits of precision e.g. \\\"10.00\\\"). The remainder will go to the

end-customer\'s Ledger balance. This must be less than or equal to the

transfer.amount.

\[\*\*network_trace_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-transfers-network-trace-id)

nullablestring

The trace identifier for the transfer based on its network. This will

only be set after the transfer has posted.\\

For ach or same-day-ach transfers, this is the ACH trace number. For rtp

transfers, this is the Transaction Identification number. For wire

transfers, this is the IMAD (Input Message Accountability Data) number.

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-list-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"transfers\\\": \\\[

{

\\\"account_id\\\": \\\"3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\",

\\\"funding_account_id\\\":
\\\"8945fedc-e703-463d-86b1-dc0607b55460\\\",

\\\"ledger_id\\\": \\\"563db5f8-4c95-4e17-8c3e-cb988fb9cf1a\\\",

\\\"ach_class\\\": \\\"ppd\\\",

\\\"amount\\\": \\\"12.34\\\",

\\\"cancellable\\\": true,

\\\"created\\\": \\\"2019-12-09T17:27:15Z\\\",

\\\"description\\\": \\\"Desc\\\",

\\\"guarantee_decision\\\": null,

\\\"guarantee_decision_rationale\\\": null,

\\\"failure_reason\\\": {

\\\"ach_return_code\\\": \\\"R13\\\",

\\\"description\\\": \\\"Invalid ACH routing number\\\"

},

\\\"id\\\": \\\"460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\",

\\\"authorization_id\\\": \\\"c9f90aa1-2949-c799-e2b6-ea05c89bb586\\\",

\\\"metadata\\\": {

\\\"key1\\\": \\\"value1\\\",

\\\"key2\\\": \\\"value2\\\"

},

\\\"network\\\": \\\"ach\\\",

\\\"origination_account_id\\\": \\\"\\\",

\\\"originator_client_id\\\": null,

\\\"refunds\\\": \\\[\\\],

\\\"status\\\": \\\"pending\\\",

\\\"type\\\": \\\"credit\\\",

\\\"iso_currency_code\\\": \\\"USD\\\",

\\\"standard_return_window\\\": \\\"2020-08-07\\\",

\\\"unauthorized_return_window\\\": \\\"2020-10-07\\\",

\\\"expected_settlement_date\\\": \\\"2020-08-04\\\",

\\\"user\\\": {

\\\"email_address\\\": \\\"acharleston@email.com\\\",

\\\"legal_name\\\": \\\"Anne Charleston\\\",

\\\"phone_number\\\": \\\"510-555-0128\\\",

\\\"address\\\": {

\\\"street\\\": \\\"123 Main St.\\\",

\\\"city\\\": \\\"San Francisco\\\",

\\\"region\\\": \\\"CA\\\",

\\\"postal_code\\\": \\\"94053\\\",

\\\"country\\\": \\\"US\\\"

}

},

\\\"recurring_transfer_id\\\": null,

\\\"credit_funds_source\\\": \\\"sweep\\\",

\\\"facilitator_fee\\\": \\\"1.23\\\",

\\\"network_trace_id\\\": null

}

\\\],

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/event/list\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfereventlist)

\[List transfer

events\](https://plaid.com/docs/api/products/transfer/reading-transfers/#list-transfer-events)

Use

the \[\*\*\[/transfer/event/list\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfereventlist) endpoint

to get a list of transfer events based on specified filter criteria.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*start_date\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-start-date)

string

The start datetime of transfers to list. This should be in RFC 3339

format (i.e. 2019-12-06T22:35:49Z)

Format: date-time

\[\*\*end_date\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-end-date)

string

The end datetime of transfers to list. This should be in RFC 3339 format

(i.e. 2019-12-06T22:35:49Z)

Format: date-time

\[\*\*transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-transfer-id)

string

Plaid\'s unique identifier for a transfer.

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-account-id)

string

The account ID to get events for all transactions to/from an account.

\[\*\*transfer_type\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-transfer-type)

string

The type of transfer. This will be either debit or credit. A debit

indicates a transfer of money into your origination account; a credit

indicates a transfer of money out of your origination account.

Possible values: debit, credit, null

\[\*\*event_types\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-event-types)

\\\[string\\\]

Filter events by event type.

Possible values: pending, cancelled, failed, posted, settled,

funds_available, returned, swept, swept_settled, return_swept,

sweep.pending, sweep.posted, sweep.settled, sweep.returned,

sweep.failed, refund.pending, refund.cancelled, refund.failed,

refund.posted, refund.settled, refund.returned, refund.swept,

refund.return_swept

\[\*\*sweep_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-sweep-id)

string

Plaid\'s unique identifier for a sweep.

\[\*\*count\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-count)

integer

The maximum number of transfer events to return. If the number of events

matching the above parameters is greater than count, the most recent

events will be returned.

Default: 25

Maximum: 25

Minimum: 1

\[\*\*offset\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-offset)

integer

The offset into the list of transfer events. When count=25 and offset=0,

the first 25 events will be returned. When count=25 and offset=25, the

next 25 events will be returned.

Default: 0

Minimum: 0

\[\*\*originator_client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-originator-client-id)

string

Filter transfer events to only those with the specified originator

client.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-request-funding-account-id)

string

Filter transfer events to only those with the specified

funding_account_id.

const request: TransferEventListRequest = {

start_date: \\\'2019-12-06T22:35:49Z\\\',

end_date: \\\'2019-12-12T22:35:49Z\\\',

transfer_id: \\\'460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\',

account_id: \\\'3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\',

transfer_type: \\\'credit\\\',

event_types: \\\[\\\'pending\\\', \\\'posted\\\'\\\],

count: 14,

offset: 2,

origination_account_id: \\\'8945fedc-e703-463d-86b1-dc0607b55460\\\',

};

try {

const response = await plaidClient.transferEventList(request);

const events = response.data.transfer_events;

for (const event of events) {

// iterate through events

}

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*transfer_events\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events)

\\\[object\\\]

Hide object

\[\*\*event_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-event-id)

integer

Plaid\'s unique identifier for this event. IDs are sequential unsigned

64-bit integers.

Minimum: 0

\[\*\*timestamp\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-timestamp)

string

The datetime when this event occurred. This will be of the form

2006-01-02T15:04:05Z.

Format: date-time

\[\*\*event_type\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-event-type)

string

The type of event that this transfer represents. Event types with prefix

sweep represents events for Plaid Ledger sweeps.\\

pending: A new transfer was created; it is in the pending state.\\

cancelled: The transfer was cancelled by the client.\\

failed: The transfer failed, no funds were moved.\\

posted: The transfer has been successfully submitted to the payment

network.\\

settled: Credits are available to be withdrawn or debits have been

deducted from the Plaid linked account.\\

funds_available: Funds from the transfer have been released from hold

and applied to the ledger\\\'s available balance. (Only applicable to
ACH

debits.)\\

returned: A posted transfer was returned.\\

swept: The transfer was swept to / from the sweep account.\\

swept_settled: Credits are available to be withdrawn or debits have been

deducted from the customer\'s business checking account.\\

return_swept: Due to the transfer being returned, funds were pulled from

or pushed back to the sweep account.\\

sweep.pending: A new ledger sweep was created; it is in the pending

state.\\

sweep.posted: The ledger sweep has been successfully submitted to the

payment network.\\

sweep.settled: The transaction has settled in the funding account. This

means that funds withdrawn from Plaid Ledger balance have reached the

funding account, or funds to be deposited into the Plaid Ledger Balance

have been pulled, and the hold period has begun.\\

sweep.returned: A posted ledger sweep was returned.\\

sweep.failed: The ledger sweep failed, no funds were moved.\\

refund.pending: A new refund was created; it is in the pending state.\\

refund.cancelled: The refund was cancelled.\\

refund.failed: The refund failed, no funds were moved.\\

refund.posted: The refund has been successfully submitted to the payment

network.\\

refund.settled: The refund transaction has settled in the Plaid linked

account.\\

refund.returned: A posted refund was returned.\\

refund.swept: The refund was swept from the sweep account.\\

refund.return_swept: Due to the refund being returned, funds were pushed

back to the sweep account.

Possible values: pending, cancelled, failed, posted, settled,

funds_available, returned, swept, swept_settled, return_swept,

sweep.pending, sweep.posted, sweep.settled, sweep.returned,

sweep.failed, refund.pending, refund.cancelled, refund.failed,

refund.posted, refund.settled, refund.returned, refund.swept,

refund.return_swept

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-account-id)

string

The account ID associated with the transfer. This field is omitted for

Plaid Ledger Sweep events.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-funding-account-id)

nullablestring

The id of the associated funding account, available in the Plaid

Dashboard. If present, this indicates which of your business checking

accounts will be credited or debited.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-transfer-id)

string

Plaid\'s unique identifier for a transfer. This field is null for Plaid

Ledger Sweep events.

\[\*\*transfer_type\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-transfer-type)

string

The type of transfer. Valid values are debit or credit. A debit

indicates a transfer of money into the origination account; a credit

indicates a transfer of money out of the origination account. This field

is omitted for Plaid Ledger Sweep events.

Possible values: debit, credit

\[\*\*transfer_amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-transfer-amount)

string

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). This field is omitted for Plaid Ledger Sweep
events.

\[\*\*failure_reason\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-failure-reason)

nullableobject

The failure reason if the event type for a transfer is \\\"failed\\\" or

\\\"returned\\\". Null value otherwise.

Hide object

\[\*\*ach_return_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-failure-reason-ach-return-code)

nullablestring

The ACH return code, e.g. R01. A return code will be provided if and

only if the transfer status is returned. For a full listing of ACH

return codes, see \[\[Transfer

errors\]{.underline}\](https://plaid.com/docs/errors/transfer/#ach-return-codes).

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-failure-reason-description)

string

A human-readable description of the reason for the failure or reversal.

\[\*\*sweep_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-sweep-id)

nullablestring

Plaid\'s unique identifier for a sweep.

\[\*\*sweep_amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-sweep-amount)

nullablestring

A signed amount of how much was swept or return_swept for this transfer

(decimal string with two digits of precision e.g. \\\"-5.50\\\").

\[\*\*refund_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-refund-id)

nullablestring

Plaid\'s unique identifier for a refund. A non-null value indicates the

event is for the associated refund of the transfer.

\[\*\*originator_client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-transfer-events-originator-client-id)

nullablestring

The Plaid client ID that is the originator of the transfer that this

event applies to. Only present if the transfer was created on behalf of

another client as a third-party sender (TPS).

\[\*\*has_more\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-has-more)

boolean

Whether there are more events to be pulled from the endpoint that have

not already been returned

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-list-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"transfer_events\\\": \\\[

{

\\\"account_id\\\": \\\"3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\",

\\\"funding_account_id\\\":
\\\"8945fedc-e703-463d-86b1-dc0607b55460\\\",

\\\"ledger_id\\\": \\\"563db5f8-4c95-4e17-8c3e-cb988fb9cf1a\\\",

\\\"transfer_amount\\\": \\\"12.34\\\",

\\\"transfer_id\\\": \\\"460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\",

\\\"transfer_type\\\": \\\"credit\\\",

\\\"event_id\\\": 1,

\\\"event_type\\\": \\\"posted\\\",

\\\"failure_reason\\\": null,

\\\"origination_account_id\\\": \\\"\\\",

\\\"originator_client_id\\\": \\\"569ed2f36b3a3a021713abc1\\\",

\\\"refund_id\\\": null,

\\\"sweep_amount\\\": null,

\\\"sweep_id\\\": null,

\\\"timestamp\\\": \\\"2019-12-09T17:27:15Z\\\"

}

\\\],

\\\"has_more\\\": true,

\\\"request_id\\\": \\\"mdqfuVxeoza6mhu\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/event/sync\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfereventsync)

\[Sync transfer

events\](https://plaid.com/docs/api/products/transfer/reading-transfers/#sync-transfer-events)

\[\*\*\[/transfer/event/sync\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfereventsync) allows

you to request up to the next 25 transfer events that happened after a

specific event_id. Use

the \[\*\*\[/transfer/event/sync\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfereventsync) endpoint

to guarantee you have seen all transfer events.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*after_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-request-after-id)

requiredinteger

The latest (largest) event_id fetched via the sync endpoint, or 0

initially.

Minimum: 0

\[\*\*count\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-request-count)

integer

The maximum number of transfer events to return.

Default: 25

Minimum: 1

Maximum: 25

const request: TransferEventListRequest = {

after_id: 4,

count: 22,

};

try {

const response = await plaidClient.transferEventSync(request);

const events = response.data.transfer_events;

for (const event of events) {

// iterate through events

}

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*transfer_events\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events)

\\\[object\\\]

Hide object

\[\*\*event_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-event-id)

integer

Plaid\'s unique identifier for this event. IDs are sequential unsigned

64-bit integers.

Minimum: 0

\[\*\*timestamp\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-timestamp)

string

The datetime when this event occurred. This will be of the form

2006-01-02T15:04:05Z.

Format: date-time

\[\*\*event_type\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-event-type)

string

The type of event that this transfer represents. Event types with prefix

sweep represents events for Plaid Ledger sweeps.\\

pending: A new transfer was created; it is in the pending state.\\

cancelled: The transfer was cancelled by the client.\\

failed: The transfer failed, no funds were moved.\\

posted: The transfer has been successfully submitted to the payment

network.\\

settled: Credits are available to be withdrawn or debits have been

deducted from the Plaid linked account.\\

funds_available: Funds from the transfer have been released from hold

and applied to the ledger\\\'s available balance. (Only applicable to
ACH

debits.)\\

returned: A posted transfer was returned.\\

swept: The transfer was swept to / from the sweep account.\\

swept_settled: Credits are available to be withdrawn or debits have been

deducted from the customer\'s business checking account.\\

return_swept: Due to the transfer being returned, funds were pulled from

or pushed back to the sweep account.\\

sweep.pending: A new ledger sweep was created; it is in the pending

state.\\

sweep.posted: The ledger sweep has been successfully submitted to the

payment network.\\

sweep.settled: The transaction has settled in the funding account. This

means that funds withdrawn from Plaid Ledger balance have reached the

funding account, or funds to be deposited into the Plaid Ledger Balance

have been pulled, and the hold period has begun.\\

sweep.returned: A posted ledger sweep was returned.\\

sweep.failed: The ledger sweep failed, no funds were moved.\\

refund.pending: A new refund was created; it is in the pending state.\\

refund.cancelled: The refund was cancelled.\\

refund.failed: The refund failed, no funds were moved.\\

refund.posted: The refund has been successfully submitted to the payment

network.\\

refund.settled: The refund transaction has settled in the Plaid linked

account.\\

refund.returned: A posted refund was returned.\\

refund.swept: The refund was swept from the sweep account.\\

refund.return_swept: Due to the refund being returned, funds were pushed

back to the sweep account.

Possible values: pending, cancelled, failed, posted, settled,

funds_available, returned, swept, swept_settled, return_swept,

sweep.pending, sweep.posted, sweep.settled, sweep.returned,

sweep.failed, refund.pending, refund.cancelled, refund.failed,

refund.posted, refund.settled, refund.returned, refund.swept,

refund.return_swept

\[\*\*account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-account-id)

string

The account ID associated with the transfer. This field is omitted for

Plaid Ledger Sweep events.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-funding-account-id)

nullablestring

The id of the associated funding account, available in the Plaid

Dashboard. If present, this indicates which of your business checking

accounts will be credited or debited.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-transfer-id)

string

Plaid\'s unique identifier for a transfer. This field is null for Plaid

Ledger Sweep events.

\[\*\*transfer_type\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-transfer-type)

string

The type of transfer. Valid values are debit or credit. A debit

indicates a transfer of money into the origination account; a credit

indicates a transfer of money out of the origination account. This field

is omitted for Plaid Ledger Sweep events.

Possible values: debit, credit

\[\*\*transfer_amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-transfer-amount)

string

The amount of the transfer (decimal string with two digits of precision

e.g. \\\"10.00\\\"). This field is omitted for Plaid Ledger Sweep
events.

\[\*\*failure_reason\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-failure-reason)

nullableobject

The failure reason if the event type for a transfer is \\\"failed\\\" or

\\\"returned\\\". Null value otherwise.

Hide object

\[\*\*ach_return_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-failure-reason-ach-return-code)

nullablestring

The ACH return code, e.g. R01. A return code will be provided if and

only if the transfer status is returned. For a full listing of ACH

return codes, see \[\[Transfer

errors\]{.underline}\](https://plaid.com/docs/errors/transfer/#ach-return-codes).

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-failure-reason-description)

string

A human-readable description of the reason for the failure or reversal.

\[\*\*sweep_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-sweep-id)

nullablestring

Plaid\'s unique identifier for a sweep.

\[\*\*sweep_amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-sweep-amount)

nullablestring

A signed amount of how much was swept or return_swept for this transfer

(decimal string with two digits of precision e.g. \\\"-5.50\\\").

\[\*\*refund_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-refund-id)

nullablestring

Plaid\'s unique identifier for a refund. A non-null value indicates the

event is for the associated refund of the transfer.

\[\*\*originator_client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-transfer-events-originator-client-id)

nullablestring

The Plaid client ID that is the originator of the transfer that this

event applies to. Only present if the transfer was created on behalf of

another client as a third-party sender (TPS).

\[\*\*has_more\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-has-more)

boolean

Whether there are more events to be pulled from the endpoint that have

not already been returned

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-event-sync-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"transfer_events\\\": \\\[

{

\\\"account_id\\\": \\\"3gE5gnRzNyfXpBK5wEEKcymJ5albGVUqg77gr\\\",

\\\"funding_account_id\\\":
\\\"8945fedc-e703-463d-86b1-dc0607b55460\\\",

\\\"ledger_id\\\": \\\"563db5f8-4c95-4e17-8c3e-cb988fb9cf1a\\\",

\\\"transfer_amount\\\": \\\"12.34\\\",

\\\"transfer_id\\\": \\\"460cbe92-2dcc-8eae-5ad6-b37d0ec90fd9\\\",

\\\"transfer_type\\\": \\\"credit\\\",

\\\"event_id\\\": 1,

\\\"event_type\\\": \\\"pending\\\",

\\\"failure_reason\\\": null,

\\\"origination_account_id\\\": \\\"\\\",

\\\"originator_client_id\\\": null,

\\\"refund_id\\\": null,

\\\"sweep_amount\\\": null,

\\\"sweep_id\\\": null,

\\\"timestamp\\\": \\\"2019-12-09T17:27:15Z\\\"

}

\\\],

\\\"has_more\\\": true,

\\\"request_id\\\": \\\"mdqfuVxeoza6mhu\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/sweep/get\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfersweepget)

\[Retrieve a

sweep\](https://plaid.com/docs/api/products/transfer/reading-transfers/#retrieve-a-sweep)

The \[\*\*\[/transfer/sweep/get\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfersweepget) endpoint

fetches a sweep corresponding to the given sweep_id.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*sweep_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-request-sweep-id)

requiredstring

Plaid\\\'s unique identifier for the sweep (UUID) or a shortened form

consisting of the first 8 characters of the identifier (8-digit

hexadecimal string).

const request: TransferSweepGetRequest = {

sweep_id: \\\'8c2fda9a-aa2f-4735-a00f-f4e0d2d2faee\\\',

};

try {

const response = await plaidClient.transferSweepGet(request);

const sweep = response.data.sweep;

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*sweep\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep)

object

Describes a sweep of funds to / from the sweep account.\\

A sweep is associated with many sweep events (events of type swept or

return_swept) which can be retrieved by invoking the

/transfer/event/list endpoint with the corresponding sweep_id.\\

swept events occur when the transfer amount is credited or debited from

your sweep account, depending on the type of the transfer. return_swept

events occur when a transfer is returned and Plaid undoes the credit or

debit.\\

The total sum of the swept and return_swept events is equal to the

amount of the sweep Plaid creates and matches the amount of the entry on

your sweep account ledger.

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-id)

string

Identifier of the sweep.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-funding-account-id)

string

The id of the funding account to use, available in the Plaid Dashboard.

This determines which of your business checking accounts will be

credited or debited.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-created)

string

The datetime when the sweep occurred, in RFC 3339 format.

Format: date-time

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-amount)

string

Signed decimal amount of the sweep as it appears on your sweep account

ledger (e.g. \\\"-10.00\\\")\\

If amount is not present, the sweep was net-settled to zero and

outstanding debits and credits between the sweep account and Plaid are

balanced.

\[\*\*iso_currency_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-iso-currency-code)

string

The currency of the sweep, e.g. \\\"USD\\\".

\[\*\*settled\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-settled)

nullablestring

The date when the sweep settled, in the YYYY-MM-DD format.

Format: date

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-status)

nullablestring

The status of a sweep transfer\\

\\\"pending\\\" - The sweep is currently pending \\\"posted\\\" - The
sweep has

been posted \\\"settled\\\" - The sweep has settled \\\"returned\\\" -
The sweep

has been returned \\\"failed\\\" - The sweep has failed

Possible values: pending, posted, settled, returned, failed, null

\[\*\*trigger\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-trigger)

nullablestring

The trigger of the sweep\\

\\\"manual\\\" - The sweep is created manually by the customer

\\\"incoming\\\" - The sweep is created by incoming funds flow (e.g.

Incoming Wire) \\\"balance_threshold\\\" - The sweep is created by
balance

threshold setting \\\"automatic_aggregate\\\" - The sweep is created by
the

Plaid automatic aggregation process. These funds did not pass through

the Plaid Ledger balance.

Possible values: manual, incoming, balance_threshold,

automatic_aggregate

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-description)

string

The description of the deposit that will be passed to the receiving bank

(up to 10 characters). Note that banks utilize this field differently,

and may or may not show it on the bank statement.

\[\*\*network_trace_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-sweep-network-trace-id)

nullablestring

The trace identifier for the transfer based on its network. This will

only be set after the transfer has posted.\\

For ach or same-day-ach transfers, this is the ACH trace number. For rtp

transfers, this is the Transaction Identification number. For wire

transfers, this is the IMAD (Input Message Accountability Data) number.

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-get-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"sweep\\\": {

\\\"id\\\": \\\"8c2fda9a-aa2f-4735-a00f-f4e0d2d2faee\\\",

\\\"funding_account_id\\\":
\\\"8945fedc-e703-463d-86b1-dc0607b55460\\\",

\\\"ledger_id\\\": \\\"563db5f8-4c95-4e17-8c3e-cb988fb9cf1a\\\",

\\\"created\\\": \\\"2020-08-06T17:27:15Z\\\",

\\\"amount\\\": \\\"12.34\\\",

\\\"iso_currency_code\\\": \\\"USD\\\",

\\\"settled\\\": \\\"2020-08-07\\\",

\\\"status\\\": \\\"settled\\\",

\\\"network_trace_id\\\": \\\"123456789012345\\\"

},

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Was this helpful?

YesNo

\[\*\*/transfer/sweep/list\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfersweeplist)

\[List

sweeps\](https://plaid.com/docs/api/products/transfer/reading-transfers/#list-sweeps)

The \[\*\*\[/transfer/sweep/list\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfersweeplist) endpoint

fetches sweeps matching the given filters.

\*\*Request fields\*\*

\[\*\*client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-client-id)

string

Your Plaid API client_id. The client_id is required and may be provided

either in the PLAID-CLIENT-ID header or as part of a request body.

\[\*\*secret\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-secret)

string

Your Plaid API secret. The secret is required and may be provided either

in the PLAID-SECRET header or as part of a request body.

\[\*\*start_date\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-start-date)

string

The start datetime of sweeps to return (RFC 3339 format).

Format: date-time

\[\*\*end_date\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-end-date)

string

The end datetime of sweeps to return (RFC 3339 format).

Format: date-time

\[\*\*count\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-count)

integer

The maximum number of sweeps to return.

Minimum: 1

Maximum: 25

Default: 25

\[\*\*offset\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-offset)

integer

The number of sweeps to skip before returning results.

Default: 0

Minimum: 0

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-amount)

string

Filter sweeps to only those with the specified amount.

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-status)

string

The status of a sweep transfer\\

\\\"pending\\\" - The sweep is currently pending \\\"posted\\\" - The
sweep has

been posted \\\"settled\\\" - The sweep has settled \\\"returned\\\" -
The sweep

has been returned \\\"failed\\\" - The sweep has failed

Possible values: pending, posted, settled, returned, failed, null

\[\*\*originator_client_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-originator-client-id)

string

Filter sweeps to only those with the specified originator client.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-funding-account-id)

string

Filter sweeps to only those with the specified funding_account_id.

\[\*\*transfer_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-transfer-id)

string

Filter sweeps to only those with the included transfer_id.

\[\*\*trigger\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-request-trigger)

string

The trigger of the sweep\\

\\\"manual\\\" - The sweep is created manually by the customer

\\\"incoming\\\" - The sweep is created by incoming funds flow (e.g.

Incoming Wire) \\\"balance_threshold\\\" - The sweep is created by
balance

threshold setting \\\"automatic_aggregate\\\" - The sweep is created by
the

Plaid automatic aggregation process. These funds did not pass through

the Plaid Ledger balance.

Possible values: manual, incoming, balance_threshold,

automatic_aggregate

const request: TransferSweepListRequest = {

start_date: \\\'2019-12-06T22:35:49Z\\\',

end_date: \\\'2019-12-12T22:35:49Z\\\',

count: 14,

offset: 2,

};

try {

const response = await plaidClient.transferSweepList(request);

const sweeps = response.data.sweeps;

for (const sweep of sweeps) {

// iterate through sweeps

}

} catch (error) {

// handle error

}

\*\*Response fields\*\* and example

Collapse all

\[\*\*sweeps\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps)

\\\[object\\\]

Hide object

\[\*\*id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-id)

string

Identifier of the sweep.

\[\*\*funding_account_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-funding-account-id)

string

The id of the funding account to use, available in the Plaid Dashboard.

This determines which of your business checking accounts will be

credited or debited.

\[\*\*ledger_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-ledger-id)

nullablestring

Plaid\'s unique identifier for a Plaid Ledger Balance.

\[\*\*created\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-created)

string

The datetime when the sweep occurred, in RFC 3339 format.

Format: date-time

\[\*\*amount\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-amount)

string

Signed decimal amount of the sweep as it appears on your sweep account

ledger (e.g. \\\"-10.00\\\")\\

If amount is not present, the sweep was net-settled to zero and

outstanding debits and credits between the sweep account and Plaid are

balanced.

\[\*\*iso_currency_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-iso-currency-code)

string

The currency of the sweep, e.g. \\\"USD\\\".

\[\*\*settled\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-settled)

nullablestring

The date when the sweep settled, in the YYYY-MM-DD format.

Format: date

\[\*\*status\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-status)

nullablestring

The status of a sweep transfer\\

\\\"pending\\\" - The sweep is currently pending \\\"posted\\\" - The
sweep has

been posted \\\"settled\\\" - The sweep has settled \\\"returned\\\" -
The sweep

has been returned \\\"failed\\\" - The sweep has failed

Possible values: pending, posted, settled, returned, failed, null

\[\*\*trigger\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-trigger)

nullablestring

The trigger of the sweep\\

\\\"manual\\\" - The sweep is created manually by the customer

\\\"incoming\\\" - The sweep is created by incoming funds flow (e.g.

Incoming Wire) \\\"balance_threshold\\\" - The sweep is created by
balance

threshold setting \\\"automatic_aggregate\\\" - The sweep is created by
the

Plaid automatic aggregation process. These funds did not pass through

the Plaid Ledger balance.

Possible values: manual, incoming, balance_threshold,

automatic_aggregate

\[\*\*description\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-description)

string

The description of the deposit that will be passed to the receiving bank

(up to 10 characters). Note that banks utilize this field differently,

and may or may not show it on the bank statement.

\[\*\*network_trace_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-sweeps-network-trace-id)

nullablestring

The trace identifier for the transfer based on its network. This will

only be set after the transfer has posted.\\

For ach or same-day-ach transfers, this is the ACH trace number. For rtp

transfers, this is the Transaction Identification number. For wire

transfers, this is the IMAD (Input Message Accountability Data) number.

\[\*\*request_id\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer-sweep-list-response-request-id)

string

A unique identifier for the request, which can be used for

troubleshooting. This identifier, like all Plaid identifiers, is case

sensitive.

{

\\\"sweeps\\\": \\\[

{

\\\"id\\\": \\\"d5394a4d-0b04-4a02-9f4a-7ca5c0f52f9d\\\",

\\\"funding_account_id\\\":
\\\"8945fedc-e703-463d-86b1-dc0607b55460\\\",

\\\"ledger_id\\\": \\\"563db5f8-4c95-4e17-8c3e-cb988fb9cf1a\\\",

\\\"created\\\": \\\"2019-12-09T17:27:15Z\\\",

\\\"amount\\\": \\\"-12.34\\\",

\\\"iso_currency_code\\\": \\\"USD\\\",

\\\"settled\\\": \\\"2019-12-10\\\",

\\\"status\\\": \\\"settled\\\",

\\\"originator_client_id\\\": null

}

\\\],

\\\"request_id\\\": \\\"saKrIBuEB9qJZno\\\"

}

Was this helpful?

YesNo

\[Webhooks\](https://plaid.com/docs/api/products/transfer/reading-transfers/#webhooks)

\[TRANSFER_EVENTS_UPDATE\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfer_events_update)

Fired when new transfer events are available. Receiving this webhook

indicates you should fetch the new events

from \[\*\*\[/transfer/event/sync\]{.underline}\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#transfereventsync).

\[\*\*webhook_type\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#TransferEventsUpdateWebhook-webhook-type)

string

TRANSFER

\[\*\*webhook_code\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#TransferEventsUpdateWebhook-webhook-code)

string

TRANSFER_EVENTS_UPDATE

\[\*\*environment\*\*\](https://plaid.com/docs/api/products/transfer/reading-transfers/#TransferEventsUpdateWebhook-environment)

string

The Plaid environment the webhook was sent from

Possible values: sandbox, production

{

\\\"webhook_type\\\": \\\"TRANSFER\\\",

\\\"webhook_code\\\": \\\"TRANSFER_EVENTS_UPDATE\\\",

\\\"environment\\\": \\\"production\\\"

}

\*\*\[\\

\]{.underline}\*\*
