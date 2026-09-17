# Challenge: Deluxe Fraud

**Category:** Broken Access Control (Business Logic Flaw)
**Difficulty:** ⭐⭐⭐

## Description

The goal of this challenge is to obtain a Deluxe Membership in the Juice Shop without actually paying for it, by manipulating the payment request sent to the server.

## Steps to Reproduce

1. Set up Burp Suite as an intercepting proxy and configure the browser to route traffic through it.
2. Open the Juice Shop application at `127.0.0.1:3000/juiceshop`.
3. Register a new account and log in.
4. Open the sidebar and select "Deluxe Membership", then click "Become a Member".
5. Add a new payment card with fictional data and select it.
6. The "Pay" button appears disabled in the UI. Inspect the button element using the browser's DevTools and remove the `mat-ripple-disabled` and `disabled="true"` attributes to enable it in the frontend.
7. In Burp Suite, enable "Intercept" under the Proxy tab.
8. Click "Pay" to trigger the request and catch it in Burp Suite.
9. In the intercepted request body, locate the JSON payload:
```json
    {"paymentMode":"card","paymentId":7}
```
10. Modify the `paymentMode` value from `"card"` to `"paid"`.
11. Forward the modified request.
12. The application confirms the Deluxe Membership has been granted, and the challenge is marked as solved on the score board.

## Discovery Process

While going through the Deluxe Membership flow, the checkout process is not just a UI interaction — every step that sends data to the server was inspected using Burp Suite, not just the obviously interactive form fields.

When intercepting the payment request, the JSON body was reviewed for fields whose values make a security-relevant claim rather than just carrying user input. The `paymentMode` field stood out: instead of being a neutral piece of data, its value (`"card"`) implicitly asserts that a real payment method was used and processed. This raised the question of whether the server actually verifies the payment against a real transaction, or simply trusts whatever value the client sends.

Testing this by changing `paymentMode` to `"paid"` and forwarding the request confirmed the assumption: the server accepted the claim without any backend verification, granting the Deluxe Membership without an actual payment ever taking place.

## Root Cause

The DevTools manipulation in steps 6–7 only bypasses **client-side UI restrictions** and is not the actual vulnerability — it's merely how the payment request becomes accessible for interception. The real flaw is that the server **trusts the `paymentMode` field from the client without server-side validation**. No actual payment verification takes place; the backend accepts the claim that payment was made without checking it against a real transaction.

## Risks & Consequences

This is a classic example of a **Broken Access Control / Business Logic vulnerability**: the server relies on client-supplied data to make trust decisions instead of enforcing checks on its own end. In a real-world e-commerce system, this class of vulnerability could allow attackers to:

- Obtain paid features, subscriptions, or products without payment
- Bypass business rules (e.g. discounts, limits, access tiers) by manipulating request parameters
- Cause direct financial loss to the business at scale, since the exploit is trivially repeatable and automatable

The broader lesson: **never trust client-side input for security- or payment-relevant decisions** — all critical business logic must be validated and enforced server-side.


## Mitigation

- Never trust a client-supplied `paymentMode` or payment status field as proof of payment — verify all payments server-side against the actual response from the payment provider
- Implement server-side checks that confirm a transaction was successfully processed (e.g. via a payment provider's webhook or callback) before granting any paid feature or membership
- Treat the checkout/payment flow as a multi-step server-side state machine, where each step (card added, payment initiated, payment confirmed) is validated independently on the backend rather than inferred from client input
- Regularly test checkout and payment endpoints with an intercepting proxy to identify any client-controllable field that influences payment outcome


---
*This documentation is for educational purposes only, as part of a structured security training exercise.*