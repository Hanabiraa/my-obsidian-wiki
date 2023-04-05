---
created: [" 02-04-2023 20:49"]
aliases: []
tags:
- article/
---

# 60 questions about FastAPI

This article about tech interview questions on topic `FastAPI`

## 1. Что такое FastAPI?

Это современный асинхронный веб-фреймворк для построения API на базе Python 3.7+, основный на starlette. Одной из его фишек является type hints, которые используются для проверки типов объектов и возвращаемых функциями значений

## 2. Расскажите о том, как построен FastAPI, о его архитектуре

FastAPI построен на базе Starlette (это облегченный фреймворк [[ASGI]] / тулкит)

FastAPI добавляет к Starlette дополнительные функции, такие как автоматическая проверка данных, сериализация и документация.

## 3. Какие автодокументации FastAPI поддерживает изначально?
	
* SwaggerUI
* OpenAPI

## 4. Какие преимущества FastAPI перед другими фреймворками? Например Django и Flask

Перед Django:
* FastAPI - библеотка, а не фреймворк. Это дает разработчикам гибкость при создании архитектуры и планирования приложения. Но также и стоит отметить, что это несет дополнительные риски, т.к. если разработчик неопытен - он может построить плохую архитектуру, которую сложно поддерживать в будущем
* Асинхронный. Эта возможность дает дополнительную скорость обработки запросов, Django - тоже асинхронный, но только для ORM и с версии 4, DRF по прежнему является полностью синхронным, к тому же разрабатывать RestAPI на Django можно только при помощи DRF
* Не несет в себе "комплект батареек" - все, что необходимо, подключаем сами. Это помогает облегчить вес итогового продукта
* Django официально не поддерживает NoSQL базы данных. Их использование в нем не рекомендуется

Перед Flask:
1. Flask - [[WSGI]], т.е. только синхронное RestAPI
2. Flask изначально не поддерживает проверку типов данных
3. Flask изначально не поддерживает аутентификации 
4. Flask изначально не поддерживает авто-документацию (Fastapi в то же время поддерживает авто-документацию изначально - SwaggerUI/OpenAPI)
	
## 5. Что такое uvicorn? Какие преимущества и недостатки?
	
Это веб-сервер, разработанный на питоне и поддерживающий протокол [[ASGI]].

Как преимущество - поддержка WebSockets
Как недостаток выделю - поддержку только HTTP/1.1
	
## 6. Можете ли вы сказать мне, что такое automatic validation? Как это работает? 
	
Автоматическая проверка — это одна из фичей FastAPI, которая автоматически проверяет входящие запросы на соответствие определенной схеме. Это гарантирует, что обрабатываются только действительные запросы, и помогает предотвратить злонамеренный ввод, который может привести к ошибкам или неожиданному поведению.

Обычно это достигается посредством Pydantic.

## 7. Как создать основное приложение FastAPI?
```Python
from fastapi import FastAPI

my_awesome_api = FastAPI() 

@my_awesome_api.get("/") 
async def root(): 
	return {"message": "Hello World"}
```

And put it in a file `main.py`, then you would call `uvicorn` like:

![[Pasted image 20230402213827.png]]

## 8. Расскажите про основные HTTP методы:

* [[HTTP METHOD GET|GET]]
* [[HTTP METHOD POST|POST]]
* [[HTTP METHOD PUT|PUT]]
* [[HTTP METHOD DELETE|DELETE]]

Более экзотические:

* [[HTTP METHOD OPTIONS|OPTIONS]]
* [[HTTP METHOD HEAD|HEAD]]
* [[HTTP METHOD PATCH|PATCH]]
* [[HTTP METHOD TRACE|TRACE]]

## 9. Как добавить самый простой ендпоинт в FastAPI?
	
```Python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
	return {"message": "Hello World"}
```

## 10. Как добавить ендпоинт в FastAPI с динамическим путем? (т.е. на конце ендпоинта например у нас динамическкая переменная, зависящая например от id чего-нибудь)

```Python
from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id: int):
	return {"item_id": item_id}
```

## 11. Что такое pydantic? зачем он нужен?

Pydantic -  это библиотека для проверки данных и управление настройками с использованием аннотаций типа Python.

Вся проверка данных выполняется Pydantic «под капотом» FastAPI. 

## 12. Предположим мы хотим иметь ендпоинт и с path-parameter, но хотим задать ограничения на такие параметры. Приведите способ это сделать
	
```Python
from enum import Enum

from fastapi import FastAPI


class ModelName(str, Enum):
	alexnet = "alexnet"
	resnet = "resnet"
	lenet = "lenet"


app = FastAPI()


@app.get("/models/{model_name}")
async def get_model(model_name: ModelName):
	if model_name is ModelName.alexnet:
		return {"model_name": model_name, "message": "Deep Learning FTW!"}

	if model_name.value == "lenet":
		return {"model_name": model_name, "message": "LeCNN all the images"}

	return {"model_name": model_name, "message": "Have some residuals"}
```

## 13. А если хотим тоже самое, но указать как path parameter - путь до файла?

Using an option directly from Starlette you can declare a _path parameter_ containing a _path_ using a URL like:

`/files/{file_path:path}`

In this case, the name of the parameter is `file_path`, and the last part, `:path`, tells it that the parameter should match any _path_.

```Python
from fastapi import FastAPI

app = FastAPI()


@app.get("/files/{file_path:path}")
async def read_file(file_path: str):
	return {"file_path": file_path}
```

## 14. Как создать ендпоинт с определенными полями запроса (Query Parameters)?

```Python
from fastapi import FastAPI

app = FastAPI()

fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
	return fake_items_db[skip : skip + limit]
```

The query is the set of key-value pairs that go after the `?` in a URL, separated by `&` characters.

For example, in the URL:

`http://127.0.0.1:8000/items/?skip=0&limit=10`

...the query parameters are:

-   `skip`: with a value of `0`
-   `limit`: with a value of `10`

As they are part of the URL, they are "naturally" strings.

But when you declare them with Python types (in the example above, as `int`), they are converted to that type and validated against it.

All the same process that applied for path parameters also applies for query parameters:

-   Editor support (obviously)
-   Data "parsing"
-   Data validation
-   Automatic documentation

## 15. Как создать ендпоинт с multiple path and query parameters в FastAPI?

```Python
from fastapi import FastAPI

app = FastAPI()


@app.get("/users/{user_id}/items/{item_id}")
async def read_user_item(
	user_id: int, item_id: str, q: str | None = None, short: bool = False
):
	item = {"item_id": item_id, "owner_id": user_id}
	if q:
		item.update({"q": q})
	if not short:
		item.update(
			{"description": "This is an amazing item that has a long description"}
		)
	return item
```

## 16. Как создать ендпоинт с определенным телом запроса? (т.е. запрос должен содержать определенные поля)

```Python
from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
	name: str
	description: str | None = None
	price: float
	tax: float | None = None


app = FastAPI()


@app.post("/items/")
async def create_item(item: Item):
	return item
```

With just that Python type declaration, FastAPI will:

* Read the body of the request as JSON.
* Convert the corresponding types (if needed).
* Validate the data.
	* If the data is invalid, it will return a nice and clear error, indicating exactly where and what was the incorrect data.
* Give you the received data in the parameter item.
	* As you declared it in the function to be of type Item, you will also have all the editor support (completion, etc) for all of the attributes and their types.
* Generate JSON Schema definitions for your model, you can also use them anywhere else you like if it makes sense for your project.
* Those schemas will be part of the generated OpenAPI schema, and used by the automatic documentation UIs.

## 17. Как создать ендпоинт с body + path + query parameters 

```Python
from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
	name: str
	description: str | None = None
	price: float
	tax: float | None = None


app = FastAPI()


@app.put("/items/{item_id}")
async def create_item(item_id: int, item: Item, q: str | None = None):
	result = {"item_id": item_id, **item.dict()}
	if q:
		result.update({"q": q})
	return result
```

The function parameters will be recognized as follows:

* If the parameter is also declared in the path, it will be used as a path parameter.
* If the parameter is of a singular type (like int, float, str, bool, etc) it will be interpreted as a query parameter.
* If the parameter is declared to be of the type of a Pydantic model, it will be interpreted as a request body.

## 18. Расскажите о `Query` в FastAPI и почему он полезен как query parameter и string validation?

При помощи Query можно задать дополнительную информацию для валидации параметра при реквесте

```Python
from typing import Annotated

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(
	q: Annotated[
		str | None, Query(min_length=3, max_length=50, regex="^fixedquery$")
	] = None
):
	results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
	if q:
		results.update({"q": q})
	return results
```

Как сделать это поле обязательным? использовать либо `...` либо ### Pydantic's `Required`

```Python
async def read_items(q: Annotated[str | None, Query(min_length=3)] = ...):

async def read_items(q: Annotated[str, Query(min_length=3)] = Required):
```

## 19. Расскажите о `Path` в FastAPI и почему он полезен как path parameter и numeric validation?

In the same way that you can declare more validations and metadata for query parameters with Query, you can declare the same type of validations and metadata for path parameters with Path.

The same applies for:

* gt: greater than
* le: less than or equal

```Python
from typing import Annotated

from fastapi import FastAPI, Path

app = FastAPI()


@app.get("/items/{item_id}")
async def read_items(
	item_id: Annotated[int, Path(title="The ID of the item to get", gt=0, le=1000)],
	q: str,
):
	results = {"item_id": item_id}
	if q:
		results.update({"q": q})
	return results
```

## 20. Расскажите о `Body` в FastAPI

Now that we have seen how to use `Path` and `Query`, let's see more advanced uses of request body declarations.

In the previous example, the _path operations_ would expect a JSON body with the attributes of an `Item`, like:

```json
{
	"name": "Foo",
	"description": "The pretender",
	"price": 42.0,
	"tax": 3.2
}
```

But you can also declare multiple body parameters, e.g. `item` and `user`:

```Python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
	name: str
	description: str | None = None
	price: float
	tax: float | None = None


class User(BaseModel):
	username: str
	full_name: str | None = None


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item, user: User):
	results = {"item_id": item_id, "item": item, "user": user}
	return results
```

In this case, **FastAPI** will notice that there are more than one body parameters in the function (two parameters that are Pydantic models).

So, it will then use the parameter names as keys (field names) in the body, and expect a body like:

```json
{
	"item": {
		"name": "Foo",
		"description": "The pretender",
		"price": 42.0,
		"tax": 3.2
	},
	"user": {
		"username": "dave",
		"full_name": "Dave Grohl"
	}
}
```

Также вот способ иметь единичное значение в теле запроса

```Python
@app.put("/items/{item_id}")
async def update_item(
	item_id: int, item: Item, user: User, importance: Annotated[int, Body()]
):
```

А это json схема такого запроса

```json
	{
	"item": {
		"name": "Foo",
		"description": "The pretender",
		"price": 42.0,
		"tax": 3.2
	},
	"user": {
		"username": "dave",
		"full_name": "Dave Grohl"
	},
	"importance": 5
}
```


Embed a single body parameter

Let's say you only have a single item body parameter from a Pydantic model Item.

By default, FastAPI will then expect its body directly.

But if you want it to expect a JSON with a key item and inside of it the model contents, as it does when you declare extra body parameters, you can use the special Body parameter embed:

`item: Item = Body(embed=True)`

```Python
from typing import Annotated

from fastapi import Body, FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
	name: str
	description: str | None = None
	price: float
	tax: float | None = None


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Annotated[Item, Body(embed=True)]):
	results = {"item_id": item_id, "item": item}
	return results
```

In this case FastAPI will expect a body like:

```json
{
	"item": {
		"name": "Foo",
		"description": "The pretender",
		"price": 42.0,
		"tax": 3.2
	}
}
```
instead of:

```json
{
	"name": "Foo",
	"description": "The pretender",
	"price": 42.0,
	"tax": 3.2
}
```

## 21. Допустим мы хотим задекларировать пример реквеста для SwaggerUI. Как это сделать?
* Первый вариант при помощи **Pydantic** `schema_extra`
	```Python
	from fastapi import FastAPI
	from pydantic import BaseModel
	
	app = FastAPI()
	
	
	class Item(BaseModel):
		name: str
		description: str | None = None
		price: float
		tax: float | None = None
	
		class Config:
			schema_extra = {
				"example": {
					"name": "Foo",
					"description": "A very nice Item",
					"price": 35.4,
					"tax": 3.2,
				}
			}
	
	
	@app.put("/items/{item_id}")
	async def update_item(item_id: int, item: Item):
		results = {"item_id": item_id, "item": item}
		return results
	```
* Второй вариант - использовать **Pydantic** `Field` аргументы
	```Python
	from fastapi import FastAPI
	from pydantic import BaseModel, Field
	
	app = FastAPI()
	
	
	class Item(BaseModel):
		name: str = Field(example="Foo")
		description: str | None = Field(default=None, example="A very nice Item")
		price: float = Field(example=35.4)
		tax: float | None = Field(default=None, example=3.2)
	
	
	@app.put("/items/{item_id}")
	async def update_item(item_id: int, item: Item):
		results = {"item_id": item_id, "item": item}
		return results
	```
* Третий вариант - example and examples переменных следующих функций FastAPI:
	* Path()
	* Query()
	* Header()
	* Cookie()
	* Body()
	* Form()
	* File()
		you can also declare a data example or a group of examples with additional information that will be added to OpenAPI.
	```Python
	from typing import Annotated
	
	from fastapi import Body, FastAPI
	from pydantic import BaseModel
	
	app = FastAPI()
	
	
	class Item(BaseModel):
		name: str
		description: str | None = None
		price: float
		tax: float | None = None
	
	
	@app.put("/items/{item_id}")
	async def update_item(
		item_id: int,
		item: Annotated[
			Item,
			Body(
				example={
					"name": "Foo",
					"description": "A very nice Item",
					"price": 35.4,
					"tax": 3.2,
				},
			),
		],
	):
		results = {"item_id": item_id, "item": item}
		return results
	```

## 22. Какие существуют дополнительные типы данных для реквестов и респонсов в FastAPI? (помимо int, float, str, bool)

-   `UUID`:
	* A standard "Universally Unique Identifier", common as an ID in many databases and systems.
	* In requests and responses will be represented as a `str`.
-   `datetime.datetime`:
	*  A Python `datetime.datetime`.
	* In requests and responses will be represented as a `str` in ISO 8601 format, like: `2008-09-15T15:53:00+05:00`.
-   `datetime.date`:
	*  Python `datetime.date`.
	*  In requests and responses will be represented as a `str` in ISO 8601 format, like: `2008-09-15`.
-   `datetime.time`:
	* A Python `datetime.time`.
	* In requests and responses will be represented as a `str` in ISO 8601 format, like: `14:23:55.003`.
-   `datetime.timedelta`:
	* A Python `datetime.timedelta`.
	* In requests and responses will be represented as a `float` of total seconds.
	* Pydantic also allows representing it as a "ISO 8601 time diff encoding", [see the docs for more info](https://pydantic-docs.helpmanual.io/usage/exporting_models/#json_encoders).
-   `frozenset`:
	* In requests and responses, treated the same as a `set`:
		* In requests, a list will be read, eliminating duplicates and converting it to a `set`.
		* In responses, the `set` will be converted to a `list`.
		* The generated schema will specify that the `set` values are unique (using JSON Schema's `uniqueItems`).
-   `bytes`:
	* Standard Python `bytes`.
	* In requests and responses will be treated as `str`.
	* The generated schema will specify that it's a `str` with `binary` "format".
-   `Decimal`:
	* Standard Python `Decimal`.
	* In requests and responses, handled the same as a `float`.
	
-   You can check all the valid pydantic data types here: [Pydantic data types](https://pydantic-docs.helpmanual.io/usage/types).

Example:

```Python
from datetime import datetime, time, timedelta
from typing import Annotated
from uuid import UUID

from fastapi import Body, FastAPI

app = FastAPI()


@app.put("/items/{item_id}")
async def read_items(
	item_id: UUID,
	start_datetime: Annotated[datetime | None, Body()] = None,
	end_datetime: Annotated[datetime | None, Body()] = None,
	repeat_at: Annotated[time | None, Body()] = None,
	process_after: Annotated[timedelta | None, Body()] = None,
):
	start_process = start_datetime + process_after
	duration = end_datetime - start_process
	return {
		"item_id": item_id,
		"start_datetime": start_datetime,
		"end_datetime": end_datetime,
		"repeat_at": repeat_at,
		"process_after": process_after,
		"start_process": start_process,
		"duration": duration,
	}
```


## 23. Как установить куки при помощи fastAPI? (иначе говоря Response Coockie)
* Use a `Response` parameter

	You can declare a parameter of type Response in your path operation function.	
	And then you can set cookies in that temporal response object.
	```Python
	from fastapi import FastAPI, Response
	
	app = FastAPI()
	
	
	@app.post("/cookie-and-object/")
	def create_cookie(response: Response):
		response.set_cookie(key="fakesession", value="fake-cookie-session-value")
		return {"message": "Come to the dark side, we have cookies"}
	```
	And then you can return any object you need, as you normally would (a `dict`, a database model, etc).
	
	And if you declared a `response_model`, it will still be used to filter and convert the object you returned.
	
	FastAPI will use that temporal response to extract the cookies (also headers and status code), and will put them in the final response that contains the value you returned, filtered by any `response_model`.
	
	You can also declare the `Response` parameter in dependencies, and set cookies (and headers) in them.

* Return a `Response` directly

	You can also create cookies when returning a `Response` directly in your code.
	
	To do that, you can create a response as described in Return a Response Directly.
	
	Then set Cookies in it, and then return it:

	```Python
	from fastapi import FastAPI
	from fastapi.responses import JSONResponse
	
	app = FastAPI()
	
	
	@app.post("/cookie/")
	def create_cookie():
		content = {"message": "Come to the dark side, we have cookies"}
		response = JSONResponse(content=content)
		response.set_cookie(key="fakesession", value="fake-cookie-session-value")
		return response
	```

## 24. Как получить Coockie на сервере FastAPI? (иначе говоря запросить куки с клиента)
```Python
from typing import Annotated

from fastapi import Cookie, FastAPI

app = FastAPI()


@app.get("/items/")
async def read_items(ads_id: Annotated[str | None, Cookie()] = None):
	return {"ads_id": ads_id}
```

## 25. Как установить headers при помощи fastAPI? (иначе говоря Response Headers)
* Use a `Response` parameter

	```Python
	from fastapi import FastAPI, Response

	app = FastAPI()
	
	
	@app.get("/headers-and-object/")
	def get_headers(response: Response):
		response.headers["X-Cat-Dog"] = "alone in the world"
		return {"message": "Hello World"}
	```

* Return a `Response` directly

	```Python
	from fastapi import FastAPI
	from fastapi.responses import JSONResponse
	
	app = FastAPI()
	
	
	@app.get("/headers/")
	def get_headers():
		content = {"message": "Hello World"}
		headers = {"X-Cat-Dog": "alone in the world", "Content-Language": "en-US"}
		return JSONResponse(content=content, headers=headers)
	```

**Have in mind that custom proprietary headers can be added [using the 'X-' prefix](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers).**

## 26. Как получить Header поля реквеста?
```Python
from typing import Annotated

from fastapi import FastAPI, Header

app = FastAPI()


@app.get("/items/")
async def read_items(user_agent: Annotated[str | None, Header()] = None):
	return {"User-Agent": user_agent}
```

`Header` has a little extra functionality on top of what `Path`, `Query` and `Cookie` provide.

Most of the standard headers are separated by a "hyphen" character, also known as the "minus symbol" (`-`).

But a variable like `user-agent` is invalid in Python.

So, by default, `Header` will convert the parameter names characters from underscore (`_`) to hyphen (`-`) to extract and document the headers.

Also, HTTP headers are case-insensitive, so, you can declare them with standard Python style (also known as "snake_case").

So, you can use `user_agent` as you normally would in Python code, instead of needing to capitalize the first letters as `User_Agent` or something similar.

If for some reason you need to disable automatic conversion of underscores to hyphens, set the parameter `convert_underscores` of `Header` to `False`:

```Python
from typing import Annotated

from fastapi import FastAPI, Header

app = FastAPI()


@app.get("/items/")
async def read_items(
    strange_header: Annotated[str | None, Header(convert_underscores=False)] = None
):
    return {"strange_header": strange_header}
```

It is possible to receive duplicate headers. That means, the same header with multiple values.

You can define those cases using a list in the type declaration.

You will receive all the values from the duplicate header as a Python `list`.

For example, to declare a header of `X-Token` that can appear more than once, you can write:

```Python
from typing import Annotated

from fastapi import FastAPI, Header

app = FastAPI()


@app.get("/items/")
async def read_items(x_token: Annotated[list[str] | None, Header()] = None):
    return {"X-Token values": x_token}
```

If you communicate with that _path operation_ sending two HTTP headers like:

```
X-Token: foo
X-Token: bar
```

The response would be like:

```
{
    "X-Token values": [
        "bar",
        "foo"
    ]
}
```

## 27. Расскажите о Response Model(Return type of endpoint)

при определении декоратора пути (`@app.get`, etc) в дополнительных параметрах можно указать `response_model`

FastAPI будет использовать этот возвращаемый тип для: 
* Валидации response данных. 
	* Если данные недействительны (например, у вас отсутствует поле), это означает, что код вашего приложения не работает, не возвращает то, что должен, и вместо неверных данных возвращает ошибку сервера. Таким образом, вы и ваши клиенты можете быть уверены, что они получат данные и данные в ожидаемой форме. 
* Добавит схему JSON для ответа в операции пути OpenAPI. 
	* Это будет использоваться автоматическими документациями (типа SwaggerUI). 
	* Он также будет использоваться средствами автоматической генерации клиентского кода.
	
Но самое главное: 
* Он будет ограничивать и фильтровать выходные данные в соответствии с тем, что определено в возвращаемом типе. 
	* Это особенно важно для безопасности, мы увидим больше об этом ниже.

Вы можете использовать этот параметр `response_model` в любом из операторов пути ендпоинта:

-   `@app.get()`
-   `@app.post()`
-   `@app.put()`
-   `@app.delete()`
-   etc.

```Python
from typing import Any

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None
    tags: list[str] = []


@app.post("/items/", response_model=Item)
async def create_item(item: Item) -> Any:
    return item


@app.get("/items/", response_model=list[Item])
async def read_items() -> Any:
    return [
        {"name": "Portal Gun", "price": 42.0},
        {"name": "Plumbus", "price": 32.0},
    ]
```

`response_model` receives the same type you would declare for a Pydantic model field, so, it can be a Pydantic model, but it can also be, e.g. a `list` of Pydantic models, like `List[Item]`.

### response_model Priority

If you declare both a return type and a `response_model`, the `response_model` will take priority and be used by FastAPI.

This way you can add correct type annotations to your functions even when you are returning a type different than the response model, to be used by the editor and tools like mypy. And still you can have FastAPI do the data validation, documentation, etc. using the `response_model`.

You can also use `response_model=None` to disable creating a response model for that _path operation_, you might need to do it if you are adding type annotations for things that are not valid Pydantic fields, you will see an example of that in one of the sections below.

```Python
from typing import Any

from fastapi import FastAPI
from pydantic import BaseModel, EmailStr

app = FastAPI()


class UserIn(BaseModel):
    username: str
    password: str
    email: EmailStr
    full_name: str | None = None


class UserOut(BaseModel):
    username: str
    email: EmailStr
    full_name: str | None = None


@app.post("/user/", response_model=UserOut)
async def create_user(user: UserIn) -> Any:
    return user
```

В таком случае мы не допускаем утечки пароля, потому что FastAPI отфильтрует поля

## 28. Как указать статус код респонса? Где указать? Нужно ли писать этот код цифрами или использовать что-то еще?

Использовать цифры == использоваться магические значения == **bad practise**

Вот так делать не нужно никогда (именно цифрой указывать):
```Python
@app.post("/items/", status_code=201)
```

У FastAPI есть специальный класс **status**, который в удобночитаемом виде позволяет использовать статус коды (*в том числе и для вебсокетов*)


```Python
from fastapi import FastAPI, status

app = FastAPI()


@app.post("/items/", status_code=status.HTTP_201_CREATED)
async def create_item(name: str):
    return {"name": name}
```


### Changing status code by default

Иногда могут случиться ситуации, когда предопределенный статус код лучше изменить:

кейс: 

> For example, imagine that you want to return an HTTP status code of "OK" `200` by default.
> But if the data didn't exist, you want to create it, and return an HTTP status code of "CREATED" `201`.
> But you still want to be able to filter and convert the data you return with a `response_model`.
> For those cases, you can use a `Response` parameter.

How to do it? Use `Response` parameter!

```Python
from fastapi import FastAPI, Response, status

app = FastAPI()

tasks = {"foo": "Listen to the Bar Fighters"}


@app.put("/get-or-create-task/{task_id}", status_code=200)
def get_or_create_task(task_id: str, response: Response):
    if task_id not in tasks:
        tasks[task_id] = "This didn't exist before"
        response.status_code = status.HTTP_201_CREATED
    return tasks[task_id]
```

And then you can return any object you need, as you normally would (a `dict`, a database model, etc).

And if you declared a `response_model`, it will still be used to filter and convert the object you returned.

**FastAPI** will use that _temporal_ response to extract the status code (also cookies and headers), and will put them in the final response that contains the value you returned, filtered by any `response_model`.

You can also declare the `Response` parameter in dependencies, and set the status code in them. But have in mind that the last one to be set will win.

## 29. Расскажите о статус кодах? что за коды со 100? с 200? и тд до 500ых

In HTTP, you send a numeric status code of 3 digits as part of the response.

These status codes have a name associated to recognize them, but the important part is the number.

In short:

-   `100` and above are for "Information". You rarely use them directly. Responses with these status codes cannot have a body.
-   **`200`** and above are for "Successful" responses. These are the ones you would use the most.
    -   `200` is the default status code, which means everything was "OK".
    -   Another example would be `201`, "Created". It is commonly used after creating a new record in the database.
    -   A special case is `204`, "No Content". This response is used when there is no content to return to the client, and so the response must not have a body.
-   **`300`** and above are for "Redirection". Responses with these status codes may or may not have a body, except for `304`, "Not Modified", which must not have one.
-   **`400`** and above are for "Client error" responses. These are the second type you would probably use the most.
    -   An example is `404`, for a "Not Found" response.
    -   For generic errors from the client, you can just use `400`.
-   `500` and above are for server errors. You almost never use them directly. When something goes wrong at some part in your application code, or server, it will automatically return one of these status codes.

## 30. Как работать с формами в FastAPI? Как тащить оттуда данные?

https://fastapi.tiangolo.com/tutorial/request-forms/