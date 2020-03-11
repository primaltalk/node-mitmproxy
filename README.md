# node-mitmproxy 3.x

[![npm](https://img.shields.io/npm/dt/node-mitmproxy.svg)](https://www.npmjs.com/package/node-mitmproxy)  
node-mitmproxy is a man-in-the-middle (MITM) proxy based on nodejs that supports http / https, which is convenient for penetration testing and development debugging.

This version translated from the original Chinese.

## 1. Features
1. Supports https
2. Supports configuration startup and also supports the introduction of modules into the code.

## 2. Installation

###### windows
```
    npm install node-mitmproxy -g
```
###### Mac
```
    sudo npm install node-mitmproxy -g
```

## 3. Use

#### About configuration files

###### Simple configuration:

simpleConfig.js
```
module.exports = {
    sslConnectInterceptor: (req, cltSocket, head) => true,
    requestInterceptor: (rOptions, req, res, ssl, next) => {
        console.log(`正在访问：${rOptions.protocol}//${rOptions.hostname}:${rOptions.port}`);
        console.log('cookie:', rOptions.headers.cookie);
        res.end('hello node-mitmproxy!');
        next();
    }
};

```
Effect:
<img width=500 src="./doc/img/hello_node-mitmproxy.jpg" />

[Detailed configuration instructions](https://github.com/wuchangming/node-mitmproxy#4Detailed configuration instructions)
[More examples](./example/config/)
#### Starting
```
node-mitmproxy -c simpleConfig.js
```

### Install node-mitmproxy CA root certificate
Default path to generate CA root certificate: `%username%/node-mitmproxy`

#### Installation of root certificate under PC
###### Mac
```
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ~/node-mitmproxy/node-mitmproxy.ca.crt
```
###### windows
Note: Certificate needs to be installed under ** Trusted Root Certificate Directory **
reference [issues#3](https://github.com/wuchangming/node-mitmproxy/issues/3)
```
start %HOMEPATH%/node-mitmproxy/node-mitmproxy.ca.crt
```

## Reference to the code as a nodejs module
```
var mitmproxy = require('node-mitmproxy');

mitmproxy.createProxy({
    sslConnectInterceptor: (req, cltSocket, head) => true,
    requestInterceptor: (rOptions, req, res, ssl, next) => {
        console.log(`正在访问：${rOptions.protocol}//${rOptions.hostname}:${rOptions.port}`);
        console.log('cookie:', rOptions.headers.cookie);
        res.end('Hello node-mitmproxy!');
        next();
    },
    responseInterceptor: (req, res, proxyReq, proxyRes, ssl, next) => {
        next();
    }
});
```


## 4. Configuration details

#### port
Start port (default: 6789)
```
    port: 6789
```

#### sslConnectInterceptor
Determine whether the connnect request requires a proxy. Pass in the parameter reference [http connnect](https://nodejs.org/api/http.html#http_event_connect)
```
    sslConnectInterceptor: (clientReq, clientSocket, head) => true,
```

#### requestInterceptor
Intercepting client requests / responses

Parameter Description:
1. requestOptions: client request parameters
2. clientReq: client request, refer to [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage)
3. clientRes: client response, refer to [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse)
4. ssl: whether the request is https
5. next: callback function, call this method after executing the interception logic
```
    requestInterceptor: (requestOptions, clientReq, clientRes, ssl, next) => {
        next();
    }
```
#### responseInterceptor
Intercept server requests / responses
Parameter Description:  

1. clientReq: client request, refer to [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage)
2. clientRes: client response, refer to [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse)
3. proxyRes: server request, refer to [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage)
4. proxyRes: server response, refer to [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse)
5. ssl: whether the request is https
6. next: callback function, call this method after executing the interception logic
```
    responseInterceptor: (clientReq, clientRes, proxyRes, proxyRes, ssl, next) => {
        next();
    }
```
#### caCertPath
CA root certificate path (ps: no special case, no configuration required)
Default: %HOMEPATH%/node-mitmproxy/node-mitmproxy.ca.crt
```
caCertPath: 'xxxx/xxxx.crt'
```

#### caKeyPath
CA root certificate key path (ps: no special case, no configuration required)
Default: %HOMEPATH%/node-mitmproxy/node-mitmproxy.ca.key.pem
```
caKeyPath: 'xxxx/xxxx.pem'
```

## 5. More
#### Logical diagram of forged https certificate
<img src="doc/img/node-MitmProxy https.png" width=650/>
