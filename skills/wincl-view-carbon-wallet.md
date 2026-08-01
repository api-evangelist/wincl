---
name: Sign in and read a WinCL carbon wallet
description: Authenticate a user, mint a JWT, and read their carbon credit balances and offset certificates.
api: openapi/wincl-openapi-original.json
operations: [oAuthSignInUsingPOST, createJWTUsingPOST, creditSummaryUsingGET, listCreditItemsUsingGET, getCreditBoxItemUsingGET, getCertificateBoxItemUsingGET]
generated: '2026-07-21'
method: generated
source: openapi/wincl-openapi-original.json
---

# Sign in and read a WinCL carbon wallet

Authenticate against the Wincl API and read a user's carbon holdings. Base URL
`https://api.wincl.io`.

## Steps

1. **Sign in** — `POST /api/v1/auth/signin` (`oAuthSignInUsingPOST`) with the user's
   social/OAuth credentials, or mint a token directly with `POST /api/v1/auth/jwt/create`
   (`createJWTUsingPOST`). Send the returned JWT as `Authorization: Bearer <JWT>` on
   every subsequent call (missing/invalid → `401 UNAUTHORIZED`).
2. **Read credit summary** — `GET /api/v1/wallet/credit` (`creditSummaryUsingGET`)
   for the user's total credit balance.
3. **List credit-box items** — `GET /api/v2/creditbox/item` (`listCreditItemsUsingGET`),
   then drill into one with `GET /api/v2/creditbox/item/{id}` (`getCreditBoxItemUsingGET`).
   A bad id returns `915 ITEM_NOT_FOUND`.
4. **List offset certificates** — read a certificate-box item with
   `getCertificateBoxItemUsingGET` to show the issued carbon-offset certificates.

## Notes
- All read endpoints are paginated with `pageNo`/`pageSize` + `sortType`
  (see `conventions/wincl-conventions.yml`).
- Errors are numeric 9xx business codes with symbolic names — see `errors/wincl-error-codes.yml`.
