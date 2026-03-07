# 1. HTTP

1. **Hyper Text Transfer Protocol** - a stateless application-layer message protocol for data exchange on web

2. It is the protocol used in <code>**Client-Server**</code> model

3. It came into existence for providing standard rules to establish communication between <code>**Client**</code> and <code>**Server**</code>
<!-- 4. HTTP messages are sent over a <code>**TLS encrypted TCP Connection**</code> for maintaining reliability and correct ordering of data  -->

## 1.1 HTTP Evolution
Let's see how HTTP evolved during the past years

### 1.1.1 WWW - World Wide Web

1. All started with **WWW (World Wide Web)** or **(called as "Mesh" earlier)** - Tim Berners Lee, build a system over internet to access/share web documents. A standard communication protocol was needed to access/share the documents on the Web, that gave birth to HTTP 

2. Building blocks of WWW:

    - <code>**HTML**</code> to represent **Hypertext** documents

    - Protocol to access/share those **Hypertext** documents <code>**(HTTP)**</code>

    - A <code>**Client**</code> to display data <code>**Request Initiator**</code>

    - A <code>**Server**</code> to handle requests from clients

### 1.1.2 HTTP/0.9: one-line protocol

- Communication between **client** and **server** happens in plain-text (human-readable format)

- Supported only 1 HTTP method - <code>**GET**</code>

- Supported only single line request
    ```js
    GET /index.html
    ```

- Server returns only HTML document
    ```html
    <html>
    <body>
        Hello World
    </body>
    </html>
    ```


## HTTP Request
    
```js
POST /users HTTP/1.1

Host: api.example.com
Content-Type: application/json
Authorization: Bearer token123
Content-Length: 27

{"name":"Lalit","age":25}
```

## HTTP Response

```js
HTTP/1.1 200 OK

Content-Type: application/json
Content-Length: 34

{"message":"User created"}
```


