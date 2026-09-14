# Challenge: Deluxe Fraud

**Category:** Broken Access Control (Business Logic Flaw)
**Difficulty:** ⭐⭐⭐ (adjust to actual rating from the score board)

## Description

The goal of this challenge is to obtain a Deluxe Membership in the Juice Shop without actually paying for it, by manipulating the payment request sent to the server.

## Steps to Reproduce

1. Set up Burp Suite as an intercepting proxy and configure the browser to route traffic through it.
2. Open the Juice Shop application at `127.0.0.1:3000/juiceshop`.
3. Register a new account and log in.
4. Open the sidebar and select "Deluxe Membership", then click "Become a Member".
5. Add a new payment card with fictional data and select it.
6. The "Pay" button remains disabled because the wallet balance is insufficient. Using the browser's DevTools (Inspect Element), locate the wallet balance field and temporarily set it to a higher value (e.g. 100) to unlock the UI.
7. Inspect the "Pay" button element and remove the `mat-ripple-disabled` and `disabled="true"` attributes to enable the button in the frontend.
8. In Burp Suite, enable "Intercept" under the Proxy tab.
9. Click "Pay" to trigger the request and catch it in Burp Suite.
10. In the intercepted request body, locate the JSON payload:
```json
    {"paymentMode":"card","paymentId":7}
```
11. Modify the `paymentMode` value from `"card"` to `"paid"`.
12. Forward the modified request.
13. The application confirms the Deluxe Membership has been granted, and the challenge is marked as solved on the score board.

## Root Cause

The DevTools manipulation in steps 6–7 only bypasses **client-side UI restrictions** and is not the actual vulnerability — it's merely how the payment request becomes accessible for interception. The real flaw is that the server **trusts the `paymentMode` field from the client without server-side validation**. No actual payment verification takes place; the backend accepts the claim that payment was made without checking it against a real transaction.

## Risks & Consequences

This is a classic example of a **Broken Access Control / Business Logic vulnerability**: the server relies on client-supplied data to make trust decisions instead of enforcing checks on its own end. In a real-world e-commerce system, this class of vulnerability could allow attackers to:

- Obtain paid features, subscriptions, or products without payment
- Bypass business rules (e.g. discounts, limits, access tiers) by manipulating request parameters
- Cause direct financial loss to the business at scale, since the exploit is trivially repeatable and automatable

The broader lesson: **never trust client-side input for security- or payment-relevant decisions** — all critical business logic must be validated and enforced server-side.

## Video

[Link to video demonstration — max. 5 minutes]

---
*This documentation is for educational purposes only, as part of a structured security training exercise.*