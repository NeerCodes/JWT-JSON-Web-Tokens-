# JWT (JSON Web Tokens) 🛡️

![Basic JWT Auth Flow](https://github.com/user-attachments/assets/325ed3dd-8c5a-4aab-bdc8-955d6b5ca3fa)

## What is JWT?

JWT (JSON Web Token) is a compact, URL-safe means of representing claims to be transferred between two parties. It is often used for **authentication** and **authorization** in web applications.

---

## 📌 Structure of JWT

A JWT is composed of three parts separated by dots (`.`):

```
<Header>.<Payload>.<Signature>
```

### 1. Header

Specifies the algorithm used for signing the token, typically:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### 2. Payload

Contains the claims — the data you want to transmit:

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

### 3. Signature

Used to verify the token’s authenticity using a secret:

```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret)
```

---

## 🔐 How JWT Works

### 1. **User Authentication**

* User submits login credentials.
* Server verifies them against the database.
* On success, server proceeds to issue a token.

### 2. **JWT Creation & Issuance**

* Server generates a JWT with user info and expiration.
* Token is signed using a secret or private key.
* JWT is sent back to the client.

### 3. **Token Usage**

* Client stores the token in **localStorage**, **sessionStorage**, or **secure cookies**.
* Client includes JWT in the `Authorization` header on subsequent requests:

```http
Authorization: Bearer <token>
```

### 4. **Token Verification**

* Server extracts the token and verifies:

  * Signature validity
  * Expiration (`exp` claim)
  * Issuer (`iss`), Audience (`aud`), Subject (`sub`), etc.
* If valid, access is granted.

### 5. **Stateless Authentication & Optional Token Refresh**

* No session data is stored on the server — fully stateless.
* On token expiry, a **refresh token** may be used to obtain a new JWT without re-login.

---

## ✅ Advantages of JWT

* Stateless and scalable (no server session storage)
* Self-contained payload (user info + claims)
* Easy to transmit via HTTP headers, cookies

## ⚠️ Security Considerations

* **Do not store JWTs in localStorage** for highly sensitive applications — vulnerable to XSS.
* Use **HTTPS** always to prevent MITM attacks.
* Short expiry tokens + refresh tokens are best practice.
* Validate all token claims (e.g., `iss`, `exp`, `aud`).

---

## 📘 Example Use Case: JWT Authentication

```js
// Node.js example using jsonwebtoken
const jwt = require('jsonwebtoken');
const secret = 'mysecret';

// Generating Token
const token = jwt.sign({ userId: 42 }, secret, { expiresIn: '1h' });

// Verifying Token
jwt.verify(token, secret, (err, decoded) => {
  if (err) return res.status(401).send('Invalid token');
  console.log(decoded.userId); // 42
});
```

---

## 📚 Common Claims in JWT

| Claim | Description                |
| ----- | -------------------------- |
| `iss` | Issuer                     |
| `sub` | Subject (user ID)          |
| `aud` | Audience                   |
| `exp` | Expiration time            |
| `nbf` | Not before                 |
| `iat` | Issued at                  |
| `jti` | JWT ID (unique identifier) |

---

## 🔄 Refresh Token Strategy

* Short-lived **Access Token**: expires quickly (e.g., 15 mins)
* Long-lived **Refresh Token**: securely stored; used to get new access token

### Flow:

1. User logs in → Access Token + Refresh Token
2. Access Token expires → send refresh token to get a new one
3. If refresh token is invalid → force re-login

---



