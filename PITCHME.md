#HTTP Cache

#VSLIDE

Since June 1999

[RFC 2616](https://tools.ietf.org/html/rfc2616)

#VSLIDE

50 percent http hits could be cached

![Image of Alexa](assets/img/cacheable-resources.png)
300 000 first sites(Alexa ranking)

#HSLIDE

### HEADERS

#### HTTP Document
![Header Struct](assets/img/http_header_struct.jpg)

#VSLIDE

### HTTP Header
![Header](assets/img/http_headers.jpg)

#VSLIDE

#### REQUEST
```HTTP
GET http://www.tiggerwigger.com/ HTTP/1.0
Proxy-Connection: Keep-Alive
User-Agent: Mozilla/5.0 [en] (X11; I; Linux 2.2.3 i686)
Host: www.tiggerwigger.com
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, image/png, */*

Accept-Encoding: gzip
Accept-Language: en
Accept-Charset: iso-8859-1, *, utf-8
```

#VSLIDE

#### RESPONSE
```HTTP
HTTP/1.0 200 OK
Date: Fri, 13 Nov 2009 06:57:43 GMT
Content-Location: http://locutus.tiggerwigger.com/index.html
Etag: "07db14afa76be1:1074"
Last-Modified: Thu, 05 Nov 2009 20:01:38 GMT
Content-Length: 7931
Content-Type: text/html
Server: Microsoft-IIS/4.0
Age: 922
Proxy-Connection: close
```

#HSLIDE

#### Good news?

Every browser ships with an implementation of an HTTP cache

![Simple cache](assets/img/http-request.png)

#VSLIDE

#### 304 Etag

Validating cached responses with ETags

![Simple Etag](assets/img/http-cache-control.png)

#VSLIDE

#### How to?

The only thing left to do is to ensure that the server is providing the necessary ETag tokens.

ExpressJs & express.static enable Etag by default

[https://up.coorpacademy.com/config](https://up.coorpacademy.com/config)

#HSLIDE

Thank you :-)

#VSLIDE

### Slides

https://github.com/CoorpAcademy/tekacademy/httpcache

### Sources
https://docs.trafficserver.apache.org/en/4.2.x/sdk/http-headers.en.html
https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=fr
