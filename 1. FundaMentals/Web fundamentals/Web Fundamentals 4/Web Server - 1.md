A [web server](https://en.wikipedia.org/wiki/Web_server) is an application that runs on the back end server, which handles all of the HTTP traffic from the client-side browser, routes it to the requested pages, and finally responds to the client-side browser. 


Web servers usually run on TCP [ports](https://en.wikipedia.org/wiki/Port_(computer_networking)) `80` or `443`, and are responsible for connecting end-users to various parts of the web application, in addition to handling their various responses.

---

## Workflow

A typical web server accepts HTTP requests from the client-side, and responds with different HTTP responses and codes, like a code `200 OK` response for a successful request, a code `404 NOT FOUND` when requesting pages that do not exist, code `403 FORBIDDEN` for requesting access to restricted pages, and so on.

![web server](https://academy.hackthebox.com/storage/modules/75/web-server-requests.jpg)

The following are some of the most common [HTTP response codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status):

Code

Description

**Successful responses**

`200 OK`

The request has succeeded

**Redirection messages**

`301 Moved Permanently`

The URL of the requested resource has been changed permanently

`302 Found`

The URL of the requested resource has been changed temporarily

**Client error responses**

`400 Bad Request`

The server could not understand the request due to invalid syntax

`401 Unauthorized`

Unauthenticated attempt to access page

`403 Forbidden`

The client does not have access rights to the content

`404 Not Found`

The server can not find the requested resource

`405 Method Not Allowed`

The request method is known by the server but has been disabled and cannot be used

`408 Request Timeout`

This response is sent on an idle connection by some servers, even without any previous request by the client

**Server error responses**

`500 Internal Server Error`

The server has encountered a situation it doesn't know how to handle

`502 Bad Gateway`

The server, while working as a gateway to get a response needed to handle the request, received an invalid response

`504 Gateway Timeout`

The server is acting as a gateway and cannot get a response in time

Web servers also accept various types of user input within HTTP requests, including text, [JSON](https://www.w3schools.com/js/js_json_intro.asp), and even binary data (i.e., for file uploads).


Many web server types can be utilized to run web applications. 

Most of these can handle all types of complex HTTP requests, and they are usually free of charge. 

We can even develop our own basic web server using languages such as `Python`, `JavaScript`, and `PHP`. 
