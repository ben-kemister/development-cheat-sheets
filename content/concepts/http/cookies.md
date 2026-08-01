---
title: "Web Security: Cookies and CSRF"
date: "2024-05-23"
tags: [Web Security, Cookies, CSRF, OAuth2, SameSite]
---

Web security mechanisms for session persistence and request integrity. 
<!--more-->

## Cookies
Cookies are small data files stored on the client side by a web server to maintain state. 
They facilitate session management, personalization, and tracking by passing data back and forth between the browser and the server.

* **Session Cookies**: Temporary files deleted once the browser session ends.
* **Persistent Cookies**: Stored for a predetermined duration to maintain settings or login state across multiple visits.
* **Common Types**:
    * `sessionid` / `PHPSESSID`: Used for active session tracking.
    * `cookie_consent`: Stores user privacy preferences.
    * Analytics/Advertising cookies (e.g., `_ga`): Used for user behavior tracking.

## CSRF Protection

Cross-Site Request Forgery (CSRF) is an attack where a malicious site tricks an authenticated user's browser into making 
unauthorized state-changing requests to a target application.

### CSRF Token Mechanism

To mitigate this, servers implement a "Synchronizer Token Pattern" involving four steps:

1. **Generation**: The server generates a unique, cryptographically strong, unpredictable token for the user's session.
2. **Embedding**: The token is embedded into the client-side state, typically via a hidden input field in HTML forms or as a header/response for APIs.
3. **Transmission**: The client includes this token in the payload or headers of any state-changing request (POST, PUT, DELETE).
4. **Validation**: The server compares the incoming token with the one stored in the user's session; if they do not match or the token is missing, the request is rejected.

This defense leverages the **Same-Origin Policy (SOP)**: while an attacker can trigger a request from a different origin, 
they cannot read the target site's content to extract the secret token.

## CSRF Cookies and `SameSite`

The `SameSite` attribute is a browser-level security control used to restrict how cookies are sent in cross-site contexts, 
serving as a defense-in-depth mechanism against CSRF.

### `SameSite` Attribute Options

* **`Strict`**: Cookies are sent only in a first-party context. If a user follows a link from an external site to your application, the cookie is withheld, and the user arrives unauthenticated.
* **`Lax`**: (Default in most modern browsers) Cookies are withheld on cross-site subrequests (e.g., images or AJAX calls) but are sent when a user performs a top-level navigation (e.g., clicking a link `<a href="...">`).
* **`None`**: Cookies are sent in all contexts, including third-party embeds. This **requires** the `Secure` attribute to be enabled.

### Common Failure Modes and Blocking

Browsers "block" cookies in various scenarios to enforce security:

* **`SameSite=None` without `Secure`**: Modern browsers (like Chrome) will reject any cookie marked `SameSite=None` if it does not also have the `Secure` attribute.
* **Third-Party Cookie Deprecation**: Browsers are increasingly blocking "third-party" cookies (cookies sent in a context different from the one that set them) by default to prevent cross-site tracking.
* **OAuth2 Callback Failures**: Errors like `No cookies were found in OAuth callback` often occur because:
    * **Protocol Mismatch**: A `Secure` cookie is sent over an unencrypted HTTP connection and is dropped by the browser.
    * **Reverse Proxy Issues**: Proxies failing to pass `X-Forwarded-Proto` can lead the application to believe a request is HTTP when it is HTTPS, causing cookie rejection.
    * **Domain Mismatch**: The `cookie_domain` attribute does not match the callback domain during redirection.

## Handy Links

* [PortSwigger: CSRF Guide](https://portswigger.net/web-security/csrf)
* [MDN: SameSite Attribute Documentation](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#samesite_attribute)
* [OWASP: CSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)

