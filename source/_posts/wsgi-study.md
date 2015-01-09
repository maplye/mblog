title: Python WSGI Study
date: 2015-01-09 09:32:18
categories: python
tags: [python,wsgi]
---

WSGI is a specification, laid out in PEP 333, for a standardized interface between Web servers and Python Web frameworks/applications.

The simple introduction about WSGI, you can get the information as below web:

* [An Introduction to the Python Web Server Gateway Interface (WSGI)](http://ivory.idyll.org/articles/wsgi-intro/what-is-wsgi.html)

* [Getting Started with WSGI](http://lucumr.pocoo.org/2007/5/21/getting-started-with-wsgi/)

# Summary of Specification

The PEP specifies three roles: the role of a server, the role of a framework/app, and the role of a middleware object.

## Web server side

The server must provide two things: an environ dictionary, and a start_response function. The environ dictionary needs to have the usual things present -- it's similar to the CGI environment. start_response is a callable that takes two arguments, status -- containing a standard HTTP status string like 200 OK -- and response_headers -- a list of standard HTTP response headers.

The Web server dispatches a request to the framework/app by calling the application:

    iterable = app(environ, start_response)
    for data in iterable:
       # send data to client

It's the framework/app's responsibility to build the headers, call start_response, and build the data returned in iterable. It's the Web server's responsibility to serve both the headers and the data up via HTTP.

## Web framework/app side

The Web framework/app is represented to the server as a Python callable. It can be a class, an object, or a function. The arguments to __init__, __call__, or the function must be as above: an environ object and a start_response callable.

The Web framework/app must call start_response before returning or yielding any data.

The Web framework/app should return any data in an iterable form -- e.g. return [ page ].

## Middleware

Middleware components must obey both the Web server side and the Web app/framework side of things, plus a few more minor niggling restrictions. Middleware should be as transparent as possible.


# First simple app

    def simple_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type','text/plain')]
        start_response(status, response_headers)
        return ['Hello world!\n']

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server
        srv = make_server('localhost', 8080, simple_app)
        srv.serve_forever()


