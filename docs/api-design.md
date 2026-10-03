# API Design Notes

## What I'm designing

This is a simplified API for a platform that lets partners handle fiat-to-crypto and crypto-to-fiat transactions.

I kept the API centered around two main things:

- Quotes — what the customer can buy or sell at a given point in time
- Orders — the actual transaction created from a quote

The API is intentionally small, but includes the pieces I would expect in a production system: authentication, validation, idempotency, rate limiting, asynchronous processing, webhooks, and consistent errors.

---

## 1. API structure

All endpoints are versioned under:

`/v1`

The main endpoints are:

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/v1/quotes` | Create a quote |
| POST | `/v1/orders` | Create an order |
| GET | `/v1/orders/{order_id}` | Get order details |
| POST | `/v1/orders/{order_id}/cancel` | Cancel an order |
| GET | `/v1/assets` | List supported assets |
| GET | `/v1/rates/limits` | Get current rates and limits |
| POST | `/v1/webhooks/test` | Test webhook delivery |

I prefer keeping the API resource-oriented rather than exposing internal services directly.

---

## 2. Authentication

Partners authenticate using an API key:

`X-API-Key: sk_live_xxxxx`

The key identifies the partner and can be associated with permissions and rate limits.

All traffic should use HTTPS.

For internal communication between services, I would use service-to-service authentication rather than reusing the public API key.

---

## 3. Quotes

A quote represents the price and amount available at a particular point in time.

For example:

```json
{
  "fiat_currency": "USD",
  "fiat_amount": "250.00",
  "crypto_currency": "BTC",
  "network": "bitcoin"
}
