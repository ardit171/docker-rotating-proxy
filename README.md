# Docker Rotating Proxy Config Generator

- Fully Optimized for Web Scraping Usage.
- HTTP/HTTPS Support
-  socks5 with Authorization Proxy to HTTP(S) proxy convert compatible by [Gost](https://github.com/ginuerzh/gost)



```
               Docker Container
               ----------------------------------
Client <---->  Squid  <-> HTTP/HTTPS Rotate Proxies---\ 
        --------------<-> Gost <-> Socks5 Proxy    --- Internet
        

It can be used in two ways.
1.Automatically control the proxy and rotate each request -> use Squid
2.Control the proxy programmatically　-> use Gost Port

```


## Usage Example

### Configuring IPs to allow the use of the Rotating Proxy
If you want to use it from outside, please specify the **your** IP address to allowed_ip.txt

http://httpbin.org/ip

Example:
```
93.184.216.34
108.62.57.53
```

### 1.Create your proxyList.txt
Search FreeProxy List or Paid/Subscribe ProxyService Provider.

example : https://github.com/clarketm/proxy-list

#### Format
```
IPAddress:Port:Type(socks5 or http or https or httpsquid):Username:Password
IPAddress:Port:Type(socks5 or http or https or httpsquid)
IPAddress:Port
```


### 2. Generate docker-compose.yml
```
cd setup && composer install && php generate.php
```
### How to it works?
![pattern1](https://user-images.githubusercontent.com/7544687/97991581-fdc2f380-1e24-11eb-99f3-df9885d627a2.png)

- Sometimes you may need the same IP address for a series of steps.
To deal with this problem, we have built a new relay server via gost.

- Most open proxies will be unavailable in a few days.
Therefore, it is useless to build a server for every open proxy, so we use squid's cache_peer to rotate a large number of open proxies.

### proxyList.txt Example1

```
127.0.0.1:1080:socks5:yourUsername:yourPassword
127.0.0.1:44129:httpsquid:mysquidproxy:mysquidpassword
127.0.0.1:29128:httpsquid:rotatingserviceUsername:password
169.254.0.1:1080:socks5:paidsocksUsername:paidsocksPassword
127.0.0.1:80
172.31.22.222:8080
```

## proxyList.txt Example2
Here are some practical examples.

using NordVPN,TorGuard,Luminati

```
89.187.161.86:80:httpsquid:yourNordVPNEmail@example.com:NordVPNPassword
173.254.222.146:1080:socks5:yourTorGuardUsername:Password
zproxy.lum-superproxy.io:22225:httpsquid:yourLuminatiUsername:Password
```




## Now try it out
```
port 3128 is rotation port.
sh-4.2# curl https://httpbin.org/ip --proxy https://127.0.0.1:3128
{
  "origin": "209.197.26.75"
}
sh-4.2# curl https://httpbin.org/ip --proxy https://127.0.0.1:3128
{
  "origin": "185.155.98.168"
}
sh-4.2# curl https://httpbin.org/ip --proxy https://127.0.0.1:3128
{
  "origin": "173.245.217.48"
}
sh-4.2# curl https://httpbin.org/ip --proxy https://127.0.0.1:3128
{
  "origin": "81.171.85.49"
}
sh-4.2# 

and.. try static ip gateway

# curl httpbin.org/ip --proxy http://127.0.0.1:49152
{
  "origin": "139.99.54.109"
}
# curl httpbin.org/ip --proxy http://127.0.0.1:49152
{
  "origin": "139.99.54.109"
}

# curl httpbin.org/ip --proxy http://127.0.0.1:49153
{
  "origin": "159.89.206.161"
}
# curl httpbin.org/ip --proxy http://127.0.0.1:49153
{
  "origin": "159.89.206.161"
}
```

## TODO
- setup/* need dockerization!! pull request welcome