---
created:
  - " 03-08-2024 17:52"
aliases: http
tags:
  - article/
---

# HTTP requests

## Each http request contains

- first line
	- method [POST/GET/PUT/DELETE]
		- POST - send data to a server
		- GET - get data from the server
		- PUT - change existing data on the server
		- DELETE - delete data from the server
	- path - name of a relative path where a query is heading
	- http version (standard, http 1.1, 2.0)
	- etc.
- headers - key-value lists
	- address
	- data
	- format specifications
	- user-agent
	- e.g. ContentType, ContentLength
- body

> ![[Pasted image 20240803175348.png]]

# HTTP responses

- Has specified structure which represents the response from a server
- Has return codes

## Error codes
1XX - informational
2XX - OK
3XX - redirect to another server
4XX - error from user side
5XX - error from server side
> ![[Pasted image 20240803180101.png]]

# Python http server - Flask
> ![[Pasted image 20240803180421.png]]


Pros:
- simple and straightforward
- written in python
- can run (almost) any python code (including ml models)
Cons:
- written in python
- not secure
- doesn't scale well

