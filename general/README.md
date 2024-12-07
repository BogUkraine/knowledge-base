# General
This section covers the next topics:
- [Authentication and Authorization](#authentication-and-authorization)
  - [Token-Based Authentication](#token-based-authentication)
    - [JWT - JSON Web Token](#jwt---json-web-token)
      - [JWT Problems](#jwt-problems)
      - [Refresh token](#refresh-token)
      - [Third-party](#third-party)
    - [oAuth](#oauth---) - TODO
    - [OpenID Connect](#openid-connect---) - TODO
- [Virtualization](#virtualization) - TODO
- [Containerization](#containerization) - TODO
  - [Docker](#docker)
- [Resource Monitoring](#resource-monitoring)
  - [Metrics](#metrics)
- [VPN]
- TODO

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

### Token-Based Authentication
#### [JWT - JSON Web Token](https://jwt.io/introduction)
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

###### JWTs Are Designed for Short-Lived Tokens
Intended Use: JWTs are designed for short-lived tokens (e.g., 5 minutes or less), typically used in single sign-on (SSO) or API authorization contexts.

Problem: User sessions often need longer lifespans, making JWTs a poor fit for maintaining state over extended periods.

###### Stateless Authentication Challenges
Stateless Nature: JWTs are often promoted for "stateless authentication," where the server doesn't store session data. However:
If a token is compromised, it cannot be revoked without significant additional infrastructure (e.g., a token blacklist).

###### Security Vulnerabilities
Even though JWTs are signed, the payload is Base64Url-encoded, not encrypted. Sensitive information stored in the payload is exposed to anyone who intercepts it.

##### Refresh token
A refresh token is a long-lived credential used to obtain a new access token once the original one expires. Also, it is not sent with every request
like access token is.

How to prevent its leakage? Well, we can just decrease the chances with:
1. Store in HttpOnly Cookies - An HttpOnly cookie is a type of cookie inaccessible to client-side JavaScript, reducing the risk of theft through Cross-Site Scripting (XSS) attacks.
2. Use Short-Lived Refresh Tokens with Rotation - Each time a refresh token is used, issue a new one and invalidate the old one
3. Token binding ties a token to a specific client (e.g., a browser or device) by including unique identifiers (such as a device fingerprint or IP address) when generating the token.

##### Third-party
1. Auth0 - enterprise-grade
2. Firebase Authentication - small-to-medium apps

Reasons to choose a third-party solution:
1. TTM - time to market is much shorter, you don't need to design it on your own.
2. Security - you don't need to be aware of new breaches or potential problems and urgently fix them
on your side, as Auth0 is in control over it.
3. Feature richness - supports SSO, MFA, passwordless login, and role-based access control.

But it's not a cheap service.

##### oAuth

TODO
##### OpenID Connect
TODO

## Virtualization

TODO

## Containerization
TODO

### Docker
todo

## Resource Monitoring
### Metrics
#### LA (Load Average)

LA is a measure of the amount of computational work that a computer system performs. The load average represents the average system load over a period of time. More details [here](https://medium.com/coinmonks/decoding-load-average-in-linux-cdc98b30e0c6).

What LA says: how many processes are currently waiting their next 1/100th second time frame. Of course, it is a mean value. 
Normal when it less than amount of CPU cores

#### RAM 
Running out of RAM indicates that the server is under severe load and application performance will almost certainly be noticeable to end users.

#### SWAP
When available RAM is short or totally maxed out, Linux moves data from RAM to SWAP. High SWAP usage means that you don’t have enough RAM

Server might reboot if you will run out of SWAP

#### Disk Space
Track current disk space used versus the total disk space on the server’s hard drives, as well as the total inodes available on the drives.

#### CPU
If the CPU usage goes to 100% for all cores, then your server is thinking too hard about something. Usually when this happens for an extended period of time, your end users will notice poor performance and response times. Sites hosted on the server might become unresponsive or extremely slow.

* Idle - idle task - when nothing to do
* User - running user space processes
* System - running the kernel
* Iowait - idle, while system making disk I/O request.
* Steal - virtual CPU waits for a real CPU while the hypervisor is servicing another virtual processor
* Softirq - software interrupts
* Nice  - users' processes that have been "niced".

