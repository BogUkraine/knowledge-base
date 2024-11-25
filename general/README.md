# General
This section covers the next topics:
- [Authentication and Authorization](#authentication-and-authorization)
  - [Token-Based Authentication](#token-based-authentication)
    - [JWT - JSON Web Token](#jwt---json-web-token)
    - [JWT Problems](#jwt-problems)
    - [oAuth](#oauth---) TBD
    - [OpenID Connect](#openid-connect---) TBD
- [ ] TODO

## Authentication and Authorization
**Authentication** is the process of verifying the identity of a user or system. It's about answering the question "Who are you?". (username and password, tokens...)

**Authorization** determines what a user or system is allowed to do after authentication. It answers the question "What can you do?" (Role-based access controls or permissions assigned to users)

### Authentication types
1. Password-Based Authentication (Basic auth) - username and password encoded in base64 with a separator (usually ":")
2. Session-Based Authentication - the server maintains a session for a user after successful authentication. A session ID is generated and stored on the server, and a corresponding identifier (usually stored in a cookie) is sent to the client.
3. Multi-Factor Authentication (MFA) - combining multiple factors such as something you know (password), something you have (OTP, security token)
4. Token-Based Authentication - tokens (e.g., JWT, OAuth tokens) issued after successful login.
5. Certificate-Based Authentication - digital certificates to verify identity.
6. Biometric Authentication - Using physical characteristics such as fingerprints, face recognition, or retina scans.

#### Token-Based Authentication
##### [JWT - JSON Web Token](https://jwt.io/introduction)
JSON Web Tokens consist of three parts separated by dots (.), which are:
* Header
* Payload
* Signature

`xxxxx.yyyyy.zzzzz`

The header typically consists of two parts: the type of the token, which is JWT, and the signing algorithm being used, such as HMAC SHA256 or RSA.

The second part of the token is the payload, which contains the claims. Claims are statements about an entity (typically, the user) and additional data. There are three types of claims: registered, public, and private claims.

To create the signature part you have to take the encoded header, the encoded payload, a secret, the algorithm specified in the header, and sign that.


##### JWT Problems

> Beside the fact JWT is widley used, you should consider the article [Stop using JWTs](https://gist.github.com/samsch/0d1f3d3b4745d778f78b230cf6061452). The author proposes to use sessions instead.

JWTs Are Designed for Short-Lived Tokens</br>
Intended Use: JWTs are designed for short-lived tokens (e.g., 5 minutes or less), typically used in single sign-on (SSO) or API authorization contexts.

Problem: User sessions often need longer lifespans, making JWTs a poor fit for maintaining state over extended periods.

Stateless Authentication Challenges</br>
Stateless Nature: JWTs are often promoted for "stateless authentication," where the server doesn't store session data. However:
If a token is compromised, it cannot be revoked without significant additional infrastructure (e.g., a token blacklist).

Security Vulnerabilities</br>
Even though JWTs are signed, the payload is Base64Url-encoded, not encrypted. Sensitive information stored in the payload is exposed to anyone who intercepts it.

##### oAuth - ...

##### OpenID Connect - ...