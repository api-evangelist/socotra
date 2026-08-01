---
name: Socotra quote-to-bind
description: Create an account, build and price a quote, underwrite it, accept it, and issue a policy on the Socotra Insurance Suite API.
api: openapi/socotra-openapi-original.json
operations: [createAuthToken, createAccount, createQuote, underwriteQuote, acceptQuote, issueQuote]
---

# Socotra quote-to-bind

Use this skill to take an insurance applicant from a new account through a bound
(issued) policy on the Socotra Insurance Suite API. Every operation is scoped to a
`{tenantLocator}`.

## Auth
1. Mint a bearer access token: `POST /auth/users/tokens` (`createAuthToken`) using
   the business-account credentials (`business_account_name`, username, password).
2. Send it on every request as `Authorization: Bearer <token>`.

## Steps
1. **Create the account** — `POST /policy/{tenantLocator}/accounts` (`createAccount`)
   to establish the customer record. Keep the returned `accountLocator`.
2. **Create the quote** — `POST /policy/{tenantLocator}/quotes` (`createQuote`) for
   the account and product. (For a fast indicative price, `createQuickQuote` at
   `/policy/{tenantLocator}/quickquotes` is the quick-quote path.)
3. **Underwrite** — `PATCH /policy/{tenantLocator}/quotes/{locator}/underwrite`
   (`underwriteQuote`) once the quote is priced.
4. **Accept** — `PATCH /policy/{tenantLocator}/quotes/{locator}/accept`
   (`acceptQuote`).
5. **Issue** — `PATCH /policy/{tenantLocator}/quotes/{locator}/issue`
   (`issueQuote`) to bind the quote into an issued policy.

## Rules
- Resources are addressed by opaque `locator` ids; carry them between steps.
- List endpoints page via `offset`/`count` and return a `ListPageResponse` envelope.
- Run against the sandbox first (`https://api.sandbox.socotra.com`) with a deployed
  sandbox tenant configuration.
