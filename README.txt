Why is it dangerous to store passwords in plain text, and how does hashing prevent harm if the database is compromised?
- Storing passwords in plain text means anyone with database access can read every user's raw password directly. Cryptographic hashing functions are one-way mathematical transformations.

The login endpoint returns the same error for "user not found" and "wrong password." Why is this better than giving specific messages?
- Distinguishing between "user not found" and "wrong password" allows malicious actors to perform user enumeration attacks. Returning a single, generic error message for both scenarios forces attackers to guess both credentials blindly without giving away feedback about registered user accounts.

In your own words, what does the middleware do on every protected request, and why is it better than putting token-checking code inside every route handler?
- On every protected request, the authentication middleware intercepts the incoming request before it reaches the main handler.

It:
Reads the Authorization header.
Extracts and verifies the JSON Web Token (JWT) using the secret key.
Rejects invalid or missing tokens immediately with a 401 Unauthorized status.
Attaches the decoded user payload to the request object (req.user) and calls next() to pass control to the route handler.

Why It's Better:
Centralized Security & Maintainability (DRY): Writing token validation logic once in a middleware file avoids duplicating code across dozens of route handlers.
Reduced Human Error: Developers can easily protect any route by adding requireAuth as an argument without risking missing steps or introducing subtle validation bugs in individual endpoints.
Single Point of Change: If token verification rules change, updating the logic in the middleware automatically updates the entire application.

What is one thing that could still go wrong in this system (e.g., token theft, no HTTPS, weak secrets), and how would you address it in a real application?
- If tokens are sent over plain http:// or stored in browser localStorage, an attacker can intercept them via network sniffing or steal them through a Cross-Site Scripting (XSS) vulnerability. Because JWTs are stateless, an attacker who steals a valid token can impersonate the user until the token expires.

How to Address It in Production:
Enforce HTTPS (TLS): Use SSL/TLS encryption for all client-server communications so tokens cannot be read in transit over public Wi-Fi.
Store Tokens in HttpOnly Cookies: Instead of sending JWTs in localStorage or Authorization headers manually, store access tokens in HttpOnly, Secure, and SameSite=Strict cookies. This prevents malicious JavaScript scripts from accessing the token via XSS.
Short Expirations & Refresh Tokens: Set access tokens to expire quickly (e.g., 15 minutes) and use a secure refresh token mechanism to limit the window of damage if a token is compromised.
