# Challenge: Admin Registration

**Category:** Improper Input Validation (Mass Assignment)
**Difficulty:** ⭐⭐⭐ [insert difficulty from score board]

## Description

The goal of this challenge is to register a new user account with administrator privileges by manipulating the registration request sent to the server, exploiting the application's lack of field-level authorization on user input.

## Steps to Reproduce

1. Set up Burp Suite as an intercepting proxy and configure the browser to route traffic through it.
2. Open the Juice Shop application in the proxied browser.
3. Navigate to the registration page and fill in the standard fields (email, password, password confirmation, security question and answer).
4. In Burp Suite, enable "Intercept" under the Proxy tab.
5. Click "Register" to trigger the request and catch it in Burp Suite.
6. In the intercepted request body, the JSON payload looks like this:
```json
   {
     "email":"kazuto@kirigaya.sao",
     "password":"asdasdasd",
     "passwordRepeat":"asdasdasd",
     "securityQuestion":{
       "id":2,
       "question":"Mädchenname der Mutter?",
       "createdAt":"2026-09-10T13:54:24.816Z",
       "updatedAt":"2026-09-10T13:54:24.816Z"
     },
     "securityAnswer":"lmvbrmovbmr"
   }
```
7. Add a new field to the JSON body that is not present in the original registration form: `"role":"admin"`.
8. Forward the modified request.
9. The application responds with a "Registration completed successfully" message, confirming the request was accepted by the server.
10. Log in with the newly registered account and navigate to the profile page — the account now shows administrator status, confirming the privilege escalation was successful.

## Discovery Process

While intercepting the registration request with Burp Suite, I reviewed the JSON body field by field not just the fields visible in the registration form (email, password, security question), but the full payload sent to the server.

This made me wonder whether the server validates which fields are permitted in the request, or whether it simply persists whatever fields are present in the JSON body into the database object. Since the security question object already contained metadata fields (`id`, `createdAt`, `updatedAt`) that I never entered manually in the form, I realized the server accepts more data than the frontend actually exposes to the user.

Based on this, I added an additional `"role":"admin"` field — not present in the original form — to the request body to test whether the server would blindly accept and persist it. Forwarding the modified request confirmed this: my account was created with administrator privileges, without any server-side check on which fields I was authorized to set.

## Root Cause

This vulnerability is a classic example of **Mass Assignment**: the server takes the fields submitted in the request body and writes them directly into the corresponding database object, without validating whether the client is actually authorized to set each of those fields. 

The `role` field exists in the underlying data model (to distinguish regular users from administrators), but it is simply not displayed in the registration form's frontend. The server incorrectly assumes that a field being hidden in the UI is equivalent to it being protected from modification — instead of enforcing an explicit whitelist of fields the client is allowed to set (e.g. only `email`, `password`, `securityQuestion`, `securityAnswer`), it accepts and persists whatever is present in the request body.

## Risks & Consequences

Mass Assignment vulnerabilities are particularly dangerous because they are trivial to exploit — no advanced tooling is required beyond an intercepting proxy — while granting an attacker complete privilege escalation. In a real-world system, this class of vulnerability could allow an attacker to:

- Self-assign administrator or other elevated roles during account creation
- Modify other sensitive, non-exposed fields on any object accepting user input (e.g. account balances, permissions, ownership flags)
- Gain full control over the application, including access to other users' data, administrative functions, and system configuration

The broader lesson: backend APIs must always enforce an explicit **whitelist of allowed input fields** per endpoint, and must never assume that hiding a field in the frontend UI provides any security guarantee — all authorization decisions must be enforced server-side.


https://www.loom.com/share/1cd80d2c15ea4094875746182b903991

---
*This documentation is for educational purposes only, as part of a structured security training exercise.*