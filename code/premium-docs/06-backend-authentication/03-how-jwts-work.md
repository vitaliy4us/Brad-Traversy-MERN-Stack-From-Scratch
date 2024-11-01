# How JSON Web Tokens Work

Before we start implementing JSON web tokens, I just want to briefly talk about how they work. In web development, there are many different ways to authenticate your users. You can use cookies, sessions, JSON web tokens or a combination. You can also use Oauth, if you want users to be able to use their Google or GitHub accounts. You also have 3rd party services such as Auth0. In this course, we are going to use JSON web tokens because I want to keep this as barebones as possible and not use any 3rd party services. It also gets tricky when we have a frontend and a backend, because we need to make sure that the frontend and backend are on the same page.

## What is a JSON Web Token?

A JSON Web Token (JWT) is a secure way to share information between two parties, such as a web server and a user's browser. It consists of three parts: a header, a payload, and a signature. The payload contains information like a user's ID or role, and the signature is used to verify the information has not been tampered with.

JWTs are commonly used for authentication, which is the process of verifying a user's identity. Traditionally, when a user logs in, a JWT is generated and sent to their browser, where it is stored. The browser sends the JWT with each request to the server, allowing the server to identify the user and their permissions without having to store session data on the server.

## Storing JWTs In an HTTP-Only Cookie

When storing JWTs, it is important to keep them secure and prevent unauthorized access. There can be security issues by storing them in the client's local storage. In this course, we're going to take a different route. One common way to do this is by storing the JWT in an HTTP-only cookie. An HTTP-only cookie can only be accessed by the server, not by JavaScript running in the browser. This helps to prevent attacks like cross-site scripting (XSS), where an attacker could steal the JWT from the browser.

To store a JWT in an HTTP-only cookie, the server sets the cookie with the JWT as its value and the "httpOnly" attribute. This attribute ensures that the cookie can only be accessed via HTTP requests, not JavaScript code. When the browser makes a request to the server, the cookie is automatically sent along with the request headers, allowing the server to identify the user and their permissions.

Overall, storing JWTs in an HTTP-only cookie is a secure way to store authentication information and prevent unauthorized access.

## Token Validation

When a server receives a JWT from a client, it must validate the token to ensure it is authentic and has not been tampered with. Validation includes checking the signature, decoding the payload, and verifying the expiration time (if present). If the token is invalid, the server should deny the request and require the user to log in again. We will create some middleware to do this.

### Token Expiration

JWTs can include an expiration time, which allows the server to set a time limit on how long the token is valid. This helps to prevent attacks like replay attacks, where an attacker could use a valid token long after the user has logged out. When a token expires, the user must log in again to receive a new token.

If you go to https://jwt.io, you can see exactly what a JWT looks like and the different parts.
