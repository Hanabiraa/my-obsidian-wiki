---
created: [" 02-04-2023 22:49"]
aliases: [HTTP TRACE, TRACE, method TRACE]
tags:
- terms/
---

# HTTP METHOD TRACE

## definition

HTTP Метод `TRACE` выполняет проверку обратной связи по пути к целевому ресурсу, предоставляя полезный механизм отладки

Конечный получатель запроса должен отразить полученное сообщение, исключая некоторые поля описанные ниже, назад клиенту как тело сообщения с ответом 200 (`OK`) с заголовком [`Content-Type`](https://developer.mozilla.org/ru/docs/Web/HTTP/Headers/Content-Type) `message/http`

![[Pasted image 20230402225043.png]]

## useful links
