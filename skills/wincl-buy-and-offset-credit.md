---
name: Buy and offset a carbon credit with WinCL
description: Browse marketplace products, place an order paid via Stripe, then retire (offset) the resulting carbon credit.
api: openapi/wincl-openapi-original.json
operations: [productListUsingGET, insertShoppingUsingPOST, requestOrderUsingPOST, reserveStripeOrderUsingPOST, confirmOrderUsingPOST, creditSummaryUsingGET, offsetCreditUsingPOST_1]
generated: '2026-07-21'
method: generated
source: openapi/wincl-openapi-original.json
---

# Buy and offset a carbon credit with WinCL

Use the Wincl API (`https://api.wincl.io`) to purchase a carbon-credit product and
then retire it as an offset. All calls require `Authorization: Bearer <JWT>`.

## Steps

1. **Browse products** — `productListUsingGET` to list
   purchasable carbon-credit / offset products. Note the product id and confirm its
   status is active (an inactive product yields `923 PRODUCT_STATUS_IS_NOT_ACTIVE`).
2. **Add to basket** — `POST /api/v1/shopping` (`insertShoppingUsingPOST`) with the
   product and quantity. Guard against `916 EXCEED_PURCHASABLE_AMOUNT`,
   `926 SHOPPING_BASKET_IS_FULL`, and `927 AMOUNT_MUST_BE_GRATER_THAN_ZERO`.
3. **Request the order** — `POST /api/v1/order` (`requestOrderUsingPOST`). A
   `921 SHOPPING_BASKET_IS_EMPTY` means step 2 did not persist;
   `904 INSUFFICIENT_PRODUCT_STOCK` means reduce quantity.
4. **Reserve Stripe payment** — `POST /api/v1/order/stripe/reserve`
   (`reserveStripeOrderUsingPOST`). Use a supported currency or you get
   `924 UNSUPPORTED_CURRENCY`. Do not retry a reserved order — a repeat gives
   `905 ALREADY_PROCESSED_ORDER`.
5. **Confirm the order** — after the Stripe PaymentIntent succeeds client-side,
   `POST /api/v1/order/stripe/confirm` (`confirmOrderUsingPOST`).
6. **Offset the credit** — read the balance with `GET /api/v1/wallet/credit`
   (`creditSummaryUsingGET`), then retire an amount with
   `POST /api/v1/wallet/credit/offset` (`offsetCreditUsingPOST_1`). The offset is
   recorded on Hedera and a carbon-offset certificate is issued. Amount must be > 0
   (`927 AMOUNT_MUST_BE_GRATER_THAN_ZERO`); missing item yields `915 ITEM_NOT_FOUND`.

## Notes
- No idempotency key is supported; rely on order-state errors (905) to detect replays.
- Business errors are numeric 9xx codes with symbolic names — see `errors/wincl-error-codes.yml`.
