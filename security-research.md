# Authentication Security Research

## 1. Session-based login

User log in with username and password. The backend verifies the credentials and creates a session.

Security measures:
- Hash passwords using a strong hashing algorithm (e.g., bcrypt, Argon2).
- Use HTTPS.
- Use secure cookies (HttpOnly, Secure, SameSite).
- Protect against CSRF.
- Expire sessions and invalidate them after logout.

**Risks:** Stolen passwords, brute-force attacks, stolen session cookies.

## 2. WebAuthn and PassKeys

We plan to use WebAuthn and PassKeys to support biometric verification, such as fingerprint or facial recognition, for user authentication.

During registration, the users device creates a cryptographic key pair. The backend stores the public key. During login, the device verifies the user and signs a challenge. The backend validates the response.

- Requires HTTPS in production.
- Requires support from the browser and device.
- Registration must be protected and linked to the correct user account.
- Account recovery must be considered if the user loses their device.
- The backend must validate the challenge and authentication response.

**Advantages:** Convenient and phishing-resistant authentication.

**Risks:** More complex to implement and users may lose access if they lose their device.

## 3. Fingerprint and Face Recognition

Fingerprint and facial recognition are handled locally on the users device.

Our backend does not store or receive biometric data. Instead, the device confirms the users identity and uses a cryptographic key as part of WebAuthn authentication.

This reduces the risk of exposing sensitive biometric information.

## 4. Comparison and Recommendation
| Method | Advantages | Disadvantages |
|--------|------------|---------------|
| Username/Password | Simple to implement | Passwords can be stolen. Brute-force attacks |
| Username, Password + 2FA | Adds further security layer | Extra login step. Codes can be phished |
| Username, Password +  bimetrics (WebAuthn) | Phishing-resistant, convenient | More complex to implement, device loss risk |

### Recommendation

We recommend username and password combined with WebAuthn biometric verification. Passwords are securely hashed, and biometric verification takes place on the users device. This gives us an additional security layer without storing biometric data in our backend.

The solution requires secure registration, HTTPS, session and cookie protection, CSRF protection, rate limiting, and a recovery process for lost devices.

### Login flow

                 ┌─────────────────────┐
                 │       User          │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Enter username &    │
                 │ password            │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Backend validates   │
                 │ credentials         │
                 └──────────┬──────────┘
                            │
                      Valid credentials?
                       /          \
                     No            Yes
                     │              │
                     ▼              ▼
              ┌────────────┐  ┌───────────────────┐
              │ Login      │  │ Biometric check   │
              │ rejected   │  │ (fingerprint/face)│
              └────────────┘  └─────────┬─────────┘
                                        │
                                        ▼
                              ┌──────────────────┐
                              │ Device verifies  │
                              │ user locally     │
                              └─────────┬────────┘
                                        │
                                        ▼
                              ┌──────────────────┐
                              │ WebAuthn signs   │
                              │ authentication   │
                              └─────────┬────────┘
                                        │
                                        ▼
                              ┌──────────────────┐
                              │ Backend verifies │
                              │ cryptographic    │
                              │ response         │
                              └─────────┬────────┘
                                        │
                                  Verification OK?
                                    /       \
                                  No         Yes
                                  │           │
                                  ▼           ▼
                           ┌────────────┐ ┌───────────────┐
                           │ Login      │ │ Create secure │
                           │ rejected   │ │ session       │
                           └────────────┘ └───────┬───────┘
                                                  │
                                                  ▼
                                        ┌─────────────────┐
                                        │ Access incident │
                                        │ management app  │
                                        └─────────────────┘