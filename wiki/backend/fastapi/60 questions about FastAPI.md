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

Для этого в FastAPI существует специальный параметр `Form` - он отвечает за поля в теге `<form></form>`

Вот так это выглядит:

```Python
from typing import Annotated

from fastapi import FastAPI, Form

app = FastAPI()


@app.post("/login/")
async def login(username: Annotated[str, Form()], password: Annotated[str, Form()]):
    return {"username": username}
```

> **Some info**: 
> `Form` is a class that inherits directly from `Body`.

> **Tip**:
> To declare form bodies, you need to use `Form` explicitly, because without it the parameters would be interpreted as query parameters or body (JSON) parameters.

> **Technical Details**:
>   Data from forms is normally encoded using the "media type" `application/x-www-form-urlencoded`.
>   But when the form includes files, it is encoded as `multipart/form-data`. You'll read about handling files in the next chapter.
>   If you want to read more about these encodings and form fields, head to the [MDN web docs for `POST`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST).

> **Warning**
> You can declare multiple `Form` parameters in a _path operation_, but you can't also declare `Body` fields that you expect to receive as JSON, as the request will have the body encoded using `application/x-www-form-urlencoded` instead of `application/json`.
> 
> __This is not a limitation of **FastAPI**, it's part of the HTTP protocol.__

## 31. Как работать с файлами в формами в FastAPI? Какие есть методы? В чем различия между `File` и `UploadFile`, что лучше?

![[Pasted image 20230405130026.png]]

Есть два пути, как вытащить файл из формы:

* `File` - при захвате файла будет полностью грузить его в память (**плохой вариант**). 

* `UploadFile` - то, что используем всегда. Дальше про него и пойдет речь

Тут примеры использования обоих вариантов:
```Python
from typing import Annotated

from fastapi import FastAPI, File, UploadFile

app = FastAPI()


@app.post("/files/")
async def create_file(file: Annotated[bytes, File()]):
    return {"file_size": len(file)}


@app.post("/uploadfile/")
async def create_upload_file(file: UploadFile):
    return {"filename": file.filename}}
```

Что по `UploadFile`:
* Нет нужды использовать `File()` как дефолтный value параметр
* Он использует "spooled" file - aайл хранится в памяти до максимального размера, а после превышения этого ограничения будет храниться на диске.
	* Это означает, что такая функция прекрасно подходит для больших файлов, картинок, видео, бинарников и прочего, без необходимости заботиться о памяти
* есть метадата у файлов
* это file-like async интерфейс **(круто!)**
* Он предоставляет фактический объект Python `SpooledTemporaryFile`, который вы можете напрямую передать другим библиотекам, которые ожидают файлоподобный объект.

У этого класса следующие аттрибуты:

-   `filename`:`str`Оригинальное имя файла при загрузке (e.g. `myimage.jpg`).
-   `content_type`:`str` тип данных (MIME type / media type) (e.g. `image/jpeg`).
-   `file`: [`SpooledTemporaryFile`](https://docs.python.org/3/library/tempfile.html#tempfile.SpooledTemporaryFile) (a [file-like](https://docs.python.org/3/glossary.html#term-file-like-object) object). This is the actual Python file that you can pass directly to other functions or libraries that expect a "file-like" object.

Также у этого класса есть следующие `async` методы *все они используют под собой `SpooledTemporaryFile`*:

-   `write(data)`: Записывать `data` (`str` or `bytes`) в файл
-   `read(size)`: читает `size` (`int`) байтов/символов в файл
-   `seek(offset)`: переходит на ту позицию байта, которая указана  `offset` (`int`) в файле.
    -   E.g., `await myfile.seek(0)` перейдет на начало файла.
    -   This is especially useful if you run `await myfile.read()` once and then need to read the contents again.
-   `close()`: Закрывает файл

> **`async` Technical Details**:
> When you use the `async` methods, **FastAPI** runs the file methods in a threadpool and awaits for them

### Optional File Upload

You can make a file optional by using standard type annotations and setting a default value of `None`:

```Python
from typing import Annotated

from fastapi import FastAPI, File, UploadFile

app = FastAPI()


@app.post("/files/")
async def create_file(file: Annotated[bytes | None, File()] = None):
    if not file:
        return {"message": "No file sent"}
    else:
        return {"file_size": len(file)}


@app.post("/uploadfile/")
async def create_upload_file(file: UploadFile | None = None):
    if not file:
        return {"message": "No upload file sent"}
    else:
        return {"filename": file.filename}
```

### UploadFile with Additional Metadata

You can also use `File()` with `UploadFile`, for example, to set additional metadata:

```Python
@app.post("/uploadfile/")
async def create_upload_file(
    file: Annotated[UploadFile, File(description="A file read as UploadFile")],
):
    return {"filename": file.filename}
```

### Multiple File Uploads

It's possible to upload several files at the same time.

They would be associated to the same "form field" sent using "form data".

To use that, declare a list of `bytes` or `UploadFile`:

```Python
from typing import Annotated

from fastapi import FastAPI, File, UploadFile
from fastapi.responses import HTMLResponse

app = FastAPI()


@app.post("/files/")
async def create_files(files: Annotated[list[bytes], File()]):
    return {"file_sizes": [len(file) for file in files]}


@app.post("/uploadfiles/")
async def create_upload_files(files: list[UploadFile]):
    return {"filenames": [file.filename for file in files]}
```

## 32. Как происходит обработка ошибок в FastAPI?

За это отвечает `HTTPException`

> помни что вместо цифр лучше использовать `status`

```Python
from fastapi import FastAPI, HTTPException

app = FastAPI()

items = {"foo": "The Foo Wrestlers"}


@app.get("/items/{item_id}")
async def read_item(item_id: str):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return {"item": items[item_id]}
```

`raise HTTPException` в своем коде

`HTTPException` — это обычное исключение Python с дополнительными данными, относящимися к API. 

Поскольку это исключение Python, вы его не `return`, а `raise`.

Это также означает, что если вы находитесь внутри служебной функции, которую вы вызываете внутри своей функции операции пути, и вы вызываете `HTTPException` изнутри этой служебной функции, она не будет запускать остальную часть кода в функции операции пути, он немедленно завершит этот запрос и отправит клиенту ошибку HTTP из исключения `HTTPException`. 

### Add custom headers to HTTPException call

There are some situations in where it's useful to be able to add custom headers to the HTTP error. For example, for some types of security.

You probably won't need to use it directly in your code.

But in case you needed it for an advanced scenario, you can add custom headers:

```Python
from fastapi import FastAPI, HTTPException

app = FastAPI()

items = {"foo": "The Foo Wrestlers"}


@app.get("/items-header/{item_id}")
async def read_item_header(item_id: str):
    if item_id not in items:
        raise HTTPException(
            status_code=404,
            detail="Item not found",
            headers={"X-Error": "There goes my error"},
        )
    return {"item": items[item_id]}
```

## 33. Какие естб дополнительные параметры, когда мы используем декоратор для ендпоинта? (пример: `@app.post`)

### Response Status Code

You can define the (HTTP) status_code to be used in the response of your path operation.

```Python
from fastapi import FastAPI, status
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None
    tags: set[str] = set()


@app.post("/items/", response_model=Item, status_code=status.HTTP_201_CREATED)
async def create_item(item: Item):
    return item
```

### Tags

You can add tags to your _path operation_, pass the parameter `tags` with a `list` of `str` (commonly just one `str`):

```Python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None
    tags: set[str] = set()


@app.post("/items/", response_model=Item, tags=["items"])
async def create_item(item: Item):
    return item


@app.get("/items/", tags=["items"])
async def read_items():
    return [{"name": "Foo", "price": 42}]


@app.get("/users/", tags=["users"])
async def read_users():
    return [{"username": "johndoe"}]
```

They will be added to the OpenAPI schema and used by the automatic documentation interfaces:

![[Pasted image 20230405132950.png]]

### Summary and description

```Python
@app.post(
    "/items/",
    response_model=Item,
    summary="Create an item",
    description="Create an item with all the information, name, description, price, tax and a set of unique tags",
)
async def create_item(item: Item):
    return item
```

### Description from docstring

As descriptions tend to be long and cover multiple lines, you can declare the _path operation_ description in the function docstring and **FastAPI** will read it from there.

You can write [Markdown](https://en.wikipedia.org/wiki/Markdown) in the docstring, it will be interpreted and displayed correctly (taking into account docstring indentation).

```Python
@app.post("/items/", response_model=Item, summary="Create an item")
async def create_item(item: Item):
    """
    Create an item with all the information:

    - **name**: each item must have a name
    - **description**: a long description
    - **price**: required
    - **tax**: if the item doesn't have tax, you can omit this
    - **tags**: a set of unique tag strings for this item
    """
    return item
```

![[Pasted image 20230405133123.png]]

### Response description

You can specify the response description with the parameter `response_description`:

```Python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None
    tags: set[str] = set()


@app.post(
    "/items/",
    response_model=Item,
    summary="Create an item",
    response_description="The created item",
)
async def create_item(item: Item):
    """
    Create an item with all the information:

    - **name**: each item must have a name
    - **description**: a long description
    - **price**: required
    - **tax**: if the item doesn't have tax, you can omit this
    - **tags**: a set of unique tag strings for this item
    """
```

### Deprecate a path operation

If you need to mark a _path operation_ as deprecated, but without removing it, pass the parameter `deprecated`:

```Python
@app.get("/elements/", tags=["items"], deprecated=True)
async def read_elements():
    return [{"item_id": "Foo"}]
```

It will be clearly marked as deprecated in the interactive docs:
![[Pasted image 20230405133254.png]]

## 34. Необходимо конвертнуть данные в тип (dict, list), совместимый с JSON (например pydantic models), что делать?

Быстрый ответ: FastAPI предоставляет функцию `jsonable_encoder()`

### Как использовать?

Предположим у нас есть db, которая принимает только JSON-совместемые данные (словари, списки и все такое)

Для примера - тип `datetime` не является объектом, который совместим с JSON

Поэтому `datetime` должен быть переконвертирован в `str` содержащим дату, согласно ISO format.

Точно так же эта база данных не поддерживает модель `Pydantic` (объект с атрибутами), а только dict.

Вы можете использовать `jsonable_encoder` для этого.

```Python
from datetime import datetime

from fastapi import FastAPI
from fastapi.encoders import jsonable_encoder
from pydantic import BaseModel

fake_db = {}


class Item(BaseModel):
    title: str
    timestamp: datetime
    description: str | None = None


app = FastAPI()


@app.put("/items/{id}")
def update_item(id: str, item: Item):
    json_compatible_item_data = jsonable_encoder(item)
    fake_db[id] = json_compatible_item_data
```

В этом примере модель Pydantic будет преобразована в `dict`, а `datetime` — в `str`.

> The result of calling it is something that can be encoded with the Python standard [`json.dumps()`](https://docs.python.org/3/library/json.html#json.dumps).

Он не возвращает большую строку, содержащую данные в формате JSON (в виде строки). Он возвращает стандартную структуру данных Python (например, словарь) со значениями и вложенными значениями, совместимыми с JSON.

> **Note**:
> jsonable_encoder is actually used by FastAPI internally to convert data. But it is useful in many other scenarios.

## 35. Допустим нужно обновить полностью данные при помощи [[HTTP METHOD PUT|PUT]] или частично при помощи [[HTTP METHOD PATCH|PATCH]]. Как сделать это в FastAPI?

Вы можете использовать `jsonable_encoder` для преобразования входных данных в данные, которые можно сохранить в формате JSON (например, в базе данных NoSQL). Например, преобразование `datetime` в `str`.

> [[HTTP METHOD PUT|PUT]] is used to receive data that should replace the existing data.

```Python
from fastapi import FastAPI
from fastapi.encoders import jsonable_encoder
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str | None = None
    description: str | None = None
    price: float | None = None
    tax: float = 10.5
    tags: list[str] = []


items = {
    "foo": {"name": "Foo", "price": 50.2},
    "bar": {"name": "Bar", "description": "The bartenders", "price": 62, "tax": 20.2},
    "baz": {"name": "Baz", "description": None, "price": 50.2, "tax": 10.5, "tags": []},
}


@app.get("/items/{item_id}", response_model=Item)
async def read_item(item_id: str):
    return items[item_id]


@app.put("/items/{item_id}", response_model=Item)
async def update_item(item_id: str, item: Item):
    update_item_encoded = jsonable_encoder(item)
    items[item_id] = update_item_encoded
    return update_item_encoded
```

### Если необходимо использовать частичное обновление - юзаем [[HTTP METHOD PATCH|HTTP PATCH]]

> **Note**:
> PATCH is less commonly used and known than PUT.
> And many teams use only PUT, even for partial updates.
> You are free to use them however you want, FastAPI doesn't impose any restrictions.
> But this guide shows you, more or less, how they are intended to be used.
> 

#### Using Pydantic's `exclude_unset` in model.dict() parameter and `update`parameter in model.copy()

If you want to receive partial updates, it's very useful to use the parameter `exclude_unset` in Pydantic's model's `.dict()`.

Like `item.dict(exclude_unset=True)`.

That would generate a `dict` with only the data that was set when creating the `item` model, excluding default values.

Then you can use this to generate a `dict` with only the data that was set (sent in the request), omitting default values.

Now, you can create a copy of the existing model using `.copy()`, and pass the `update` parameter with a `dict` containing the data to update.

Like `stored_item_model.copy(update=update_data)`:

```Python
from fastapi import FastAPI
from fastapi.encoders import jsonable_encoder
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str | None = None
    description: str | None = None
    price: float | None = None
    tax: float = 10.5
    tags: list[str] = []


items = {
    "foo": {"name": "Foo", "price": 50.2},
    "bar": {"name": "Bar", "description": "The bartenders", "price": 62, "tax": 20.2},
    "baz": {"name": "Baz", "description": None, "price": 50.2, "tax": 10.5, "tags": []},
}


@app.get("/items/{item_id}", response_model=Item)
async def read_item(item_id: str):
    return items[item_id]


@app.patch("/items/{item_id}", response_model=Item)
async def update_item(item_id: str, item: Item):
    stored_item_data = items[item_id]
    stored_item_model = Item(**stored_item_data)
    update_data = item.dict(exclude_unset=True)
    updated_item = stored_item_model.copy(update=update_data)
    items[item_id] = jsonable_encoder(updated_item)
    return updated_item
```

## 34. Расскажите что такое dependency injection в FastAPI

> **"Dependency Injection"** means, in programming, that there is a way for your code (in this case, your _path operation functions_) to declare things that it requires to work and use: "dependencies".

And then, that system (in this case **FastAPI**) will take care of doing whatever is needed to provide your code with those needed dependencies ("inject" the dependencies).

This is very useful when you need to:

-   Have shared logic (the same code logic again and again).
-   Share database connections.
-   Enforce security, authentication, role requirements, etc.
-   And many other things...

All these, while minimizing code repetition.

### Как использовать?

1. Создадим функцию, которую будем использовать как зависимость:

```Python
async def common_parameters(q: str | None = None, skip: int = 0, limit: int = 100):
    return {"q": q, "skip": skip, "limit": limit}
```

В этом случае эта зависимость ожидает:
* Необязательный параметр запроса `q`, представляющий собой строку `str`
* Необязательный параметр запроса `skip`, который является `int` и по умолчанию равен 0
* Необязательный `limit` параметра запроса, который является `int` и по умолчанию равен 100

И затем он просто возвращает dict, содержащий эти значения.

2. Подключаем специальную функцию FastAPI

```Python
from fastapi import Depends
```

3. Декларируем зависимость в функцию, куда должны быть "введена инъекция"

```Python
from typing import Annotated

from fastapi import Depends, FastAPI

app = FastAPI()


async def common_parameters(q: str | None = None, skip: int = 0, limit: int = 100):
    return {"q": q, "skip": skip, "limit": limit}


@app.get("/items/")
async def read_items(commons: Annotated[dict, Depends(common_parameters)]):
    return commons


@app.get("/users/")
async def read_users(commons: Annotated[dict, Depends(common_parameters)]):
    return commons
```

Although you use `Depends` in the parameters of your function the same way you use `Body`, `Query`, etc, `Depends` works a bit differently.

**You only give `Depends` a single parameter.**

This parameter must be something like a function.

You **don't call it** directly (don't add the parenthesis at the end), you just pass it as a parameter to `Depends()`.

And that function takes parameters in the same way that _path operation functions_ do.

## 35. Расскажите о dependency injection в декораторе создания ендпоинта (о параметре `dependencies` в декораторе)

In some cases you don't really need the return value of a dependency inside your _path operation function_.

Or the dependency doesn't return a value.

But you still need it to be executed/solved.

For those cases, instead of declaring a _path operation function_ parameter with `Depends`, you can add a `list` of `dependencies` to the _path operation decorator_.

### Add dependencies to the path operation decorator

```Python
from fastapi import Depends, FastAPI, Header, HTTPException
from typing_extensions import Annotated

app = FastAPI()


async def verify_token(x_token: Annotated[str, Header()]):
    if x_token != "fake-super-secret-token":
        raise HTTPException(status_code=400, detail="X-Token header invalid")


async def verify_key(x_key: Annotated[str, Header()]):
    if x_key != "fake-super-secret-key":
        raise HTTPException(status_code=400, detail="X-Key header invalid")
    return x_key


@app.get("/items/", dependencies=[Depends(verify_token), Depends(verify_key)])
async def read_items():
    return [{"item": "Foo"}, {"item": "Bar"}]
```

**These dependencies will be executed/solved the same way normal dependencies. But their value (if they return any) won't be passed to your _path operation function_.**

## 36. Мы хотим добавить глобальную dependency injection во все ендпоинты разом. Как сделать?

Засунуть ее в параметр `dependencies` при создании `FastAPI()` app

```Python
from fastapi import Depends, FastAPI, Header, HTTPException
from typing_extensions import Annotated


async def verify_token(x_token: Annotated[str, Header()]):
    if x_token != "fake-super-secret-token":
        raise HTTPException(status_code=400, detail="X-Token header invalid")


async def verify_key(x_key: Annotated[str, Header()]):
    if x_key != "fake-super-secret-key":
        raise HTTPException(status_code=400, detail="X-Key header invalid")
    return x_key


app = FastAPI(dependencies=[Depends(verify_token), Depends(verify_key)])


@app.get("/items/")
async def read_items():
    return [{"item": "Portal Gun"}, {"item": "Plumbus"}]


@app.get("/users/")
async def read_users():
    return [{"username": "Rick"}, {"username": "Morty"}]
```

## 37. Зачем делать dependency injection вместе с yield вместо return?

> **Technical Details**:
> Any function that is valid to use with:
> -   [`@contextlib.contextmanager`](https://docs.python.org/3/library/contextlib.html#contextlib.contextmanager) or
> -   [`@contextlib.asynccontextmanager`](https://docs.python.org/3/library/contextlib.html#contextlib.asynccontextmanager)
> 
> would be valid to use as a **FastAPI** dependency.
> In fact, FastAPI uses those two decorators internally.

1. Для получения курсора базы данных:

	The yielded value is what is injected into _path operations_ and other dependencies:
	
	The code following the `yield` statement is executed after the response has been delivered:
	```Python
	async def get_db():
	    db = DBSession()
	    try:
	        yield db
	    finally:
	        db.close()
	```

### о Dependencies with yield and HTTPException¶

**You can still raise exceptions including `HTTPException` _before_ the `yield`. But not after.** Потому что после все будет перехватываться и ответ будет неизменен

![[Pasted image 20230405160846.png]]

## 37. Расскажите о безопасности и как FastAPI ее обеспечивает (о `fastapi.security`)

`fastapi.security` - пакет модулей, что предоставляет некие функции для почти всех концептов для обеспечения простоты разработки

поговорим о концептах:

### OAuth2

OAuth2 — это спецификация, определяющая несколько способов проверки аутентификации и авторизации.

Это довольно обширная спецификация, которая охватывает несколько сложных вариантов использования.

Он включает способы аутентификации с использованием «третьей стороны». 

Это то, что используют все системы с «логином через Facebook, Google, Twitter, GitHub».

OAuth2 не указывает, как шифровать связь, он ожидает, что ваше приложение будет обслуживаться с помощью HTTPS.

### OAuth 1

Был OAuth 1, который сильно отличался от OAuth2 и был более сложным, так как включал в себя прямые спецификации того, как шифровать связь.

**В настоящее время он не очень популярен и не используется.**

### OpenID Connect

OpenID Connect — еще одна спецификация, основанная на OAuth2.

Он просто расширяет OAuth2, указывая некоторые вещи, относительно неоднозначные в OAuth2, чтобы попытаться сделать его более совместимым. 

Например, для входа в Google используется OpenID Connect (который в свою очередь использует OAuth2). 

Но вход в Facebook не поддерживает OpenID Connect. У него есть своя разновидность OAuth2.

### OpenID (not "OpenID Connect")

Также существовала спецификация OpenID. Это пыталось решить ту же проблему, что и OpenID Connect, но не было основано на OAuth2.

Таким образом, это была полноценная дополнительная система. 

**В настоящее время он не очень популярен и не используется.**

### OpenAPI

OpenAPI (ранее известный как Swagger) — это открытая спецификация для создания API (теперь часть Linux Foundation).

**FastAPI** основан на **OpenAPI**.

Это то, что позволяет иметь несколько автоматических интерактивных интерфейсов документации, генерацию кода и т. д.

В OpenAPI есть способ определить несколько «схем» безопасности.

Используя их, вы можете воспользоваться преимуществами всех этих стандартных инструментов, включая эти интерактивные системы документации.

OpenAPI определяет следующие схемы безопасности:
- `apiKey`: специальный ключ приложения, который может исходить из:
	- query parameter. 
	- header. 
	- cookie. 
- `http`: стандартные системы аутентификации HTTP, в том числе: 
	-  `bearer`: a header `Authorization` with a value of `Bearer` plus a token. This is inherited from OAuth2.
	-   HTTP Basic authentication.
	-   HTTP Digest, etc.
- `oauth2`: все способы обеспечения безопасности OAuth2 (called "flows"). 
	- Некоторые из этих потоков подходят для создания поставщика аутентификации OAuth 2.0 (например, Google, Facebook, Twitter, GitHub и т. д.): 
		-   `implicit`
		-   `clientCredentials`
		-   `authorizationCode`
	- Но есть один конкретный «flow», который можно идеально использовать для обработки аутентификации напрямую в том же приложении: 
		- `password`: в следующих главах будут рассмотрены примеры этого. 
- `openIdConnect`: есть способ определить, как автоматически обнаруживать данные аутентификации OAuth2. 
	- Это автоматическое обнаружение определено в спецификации OpenID Connect.


## 38. Мы хотим OAuth2. Как сделать в FastAPI?


Let's imagine that you have your **backend** API in some domain.

And you have a **frontend** in another domain or in a different path of the same domain (or in a mobile application).

And you want to have a way for the frontend to authenticate with the backend, using a **username** and **password**.

We can use **OAuth2** to build that with **FastAPI**.

Let's use the tools provided by **FastAPI** to handle security.

create main.py

```Python
from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer
from typing_extensions import Annotated

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


@app.get("/items/")
async def read_items(token: Annotated[str, Depends(oauth2_scheme)]):
    return {"token": token}
```

![[Pasted image 20230405193409.png]]

And if you click it, you have a little authorization form to type a `username` and `password` (and other optional fields):

![[Pasted image 20230405193427.png]]

This is of course not the frontend for the final users, but it's a great automatic tool to document interactively all your API.

It can be used by the frontend team (that can also be yourself).

It can be used by third party applications and systems.

And it can also be used by yourself, to debug, check and test the same application.

### The `password` flow

Now let's go back a bit and understand what is all that.

The `password` "flow" is one of the ways ("flows") defined in OAuth2, to handle security and authentication.

OAuth2 was designed so that the backend or API could be independent of the server that authenticates the user.

But in this case, the same **FastAPI** application will handle the API and the authentication.

So, let's review it from that simplified point of view:

-   The user types the `username` and `password` in the frontend, and hits `Enter`.
-   The frontend (running in the user's browser) sends that `username` and `password` to a specific URL in our API (declared with `tokenUrl="token"`).
-   The API checks that `username` and `password`, and responds with a "token" (we haven't implemented any of this yet).
    -   A "token" is just a string with some content that we can use later to verify this user.
    -   Normally, a token is set to expire after some time.
        -   So, the user will have to log in again at some point later.
        -   And if the token is stolen, the risk is less. It is not like a permanent key that will work forever (in most of the cases).
-   The frontend stores that token temporarily somewhere.
-   The user clicks in the frontend to go to another section of the frontend web app.
-   The frontend needs to fetch some more data from the API.
    -   But it needs authentication for that specific endpoint.
    -   So, to authenticate with our API, it sends a header `Authorization` with a value of `Bearer` plus the token.
    -   If the token contains `foobar`, the content of the `Authorization` header would be: `Bearer foobar`.

### FastAPI's OAuth2PasswordBearer

**FastAPI** provides several tools, at different levels of abstraction, to implement these security features.

In this example we are going to use **OAuth2**, with the **Password** flow, using a **Bearer** token. We do that using the `OAuth2PasswordBearer` class.

> **Info**
> A "bearer" token is not the only option
> But it's the best one for our use case
> And it might be the best for most use cases, unless you are an OAuth2 expert and know exactly why there's another option that suits better your needs
> In that case, **FastAPI** also provides you with the tools to build it

When we create an instance of the `OAuth2PasswordBearer` class we pass in the `tokenUrl` parameter. This parameter contains the URL that the client (the frontend running in the user's browser) will use to send the `username` and `password` in order to get a token.

```Python
from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer
from typing_extensions import Annotated

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


@app.get("/items/")
async def read_items(token: Annotated[str, Depends(oauth2_scheme)]):
    return {"token": token}
```

> **Tip**:
> Here `tokenUrl="token"` refers to a relative URL token that we haven't created yet. As it's a relative URL, it's equivalent to `./token`.
> Because we are using a relative URL, if your API was located at `https://example.com/`, then it would refer to `https://example.com/token`. But if your API was located at `https://example.com/api/v1/`, then it would refer to `https://example.com/api/v1/token`.
> Using a relative URL is important to make sure your application keeps working even in an advanced use case like Behind a Proxy.

This parameter doesn't create that endpoint / _path operation_, but declares that the URL `/token` will be the one that the client should use to get the token. That information is used in OpenAPI, and then in the interactive API documentation systems.

We will soon also create the actual path operation.

> **Info**:
> If you are a very strict "Pythonista" you might dislike the style of the parameter name `tokenUrl` instead of `token_url`.
> That's because it is using the same name as in the OpenAPI spec. So that if you need to investigate more about any of these security schemes you can just copy and paste it to find more information about it.

The `oauth2_scheme` variable is an instance of `OAuth2PasswordBearer`, but it is also a "callable".

It could be called as:

`oauth2_scheme(some, parameters)`

So, it can be used with `Depends`.

Now you can pass that `oauth2_scheme` in a dependency with `Depends`.

```Python
from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer
from typing_extensions import Annotated

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


@app.get("/items/")
async def read_items(token: Annotated[str, Depends(oauth2_scheme)]):
    return {"token": token}
```

This dependency will provide a `str` that is assigned to the parameter `token` of the _path operation function_.

**FastAPI** will know that it can use this dependency to define a "security scheme" in the OpenAPI schema (and the automatic API docs).

> **Technical Details**:
> **FastAPI** will know that it can use the class `OAuth2PasswordBearer` (declared in a dependency) to define the security scheme in OpenAPI because it inherits from `fastapi.security.oauth2.OAuth2`, which in turn inherits from `fastapi.security.base.SecurityBase`.
> All the security utilities that integrate with OpenAPI (and the automatic API docs) inherit from `SecurityBase`, that's how **FastAPI** can know how to integrate them in OpenAPI.

### What it does

It will go and look in the request for that `Authorization` header, check if the value is `Bearer` plus some token, and will return the token as a `str`.

If it doesn't see an `Authorization` header, or the value doesn't have a `Bearer` token, it will respond with a 401 status code error (`UNAUTHORIZED`) directly.

You don't even have to check if the token exists to return an error. You can be sure that if your function is executed, it will have a `str` in that token.

You can try it already in the interactive docs:

![[Pasted image 20230405194843.png]]

## 39. Допустим мы хотим через dependency injection получать юзера, исходя из токена при OAuth2. Как сделать?

### Create a user model

rst, let's create a Pydantic user model.

The same way we use Pydantic to declare bodies, we can use it anywhere else:

```Python
class User(BaseModel):
    username: str
    email: Union[str, None] = None
    full_name: Union[str, None] = None
    disabled: Union[bool, None] = None
```

### Create a get_current_user dependency

Let's create a dependency `get_current_user`.

Remember that dependencies can have sub-dependencies?

`get_current_user` will have a dependency with the same `oauth2_scheme` we created before.

The same as we were doing before in the _path operation_ directly, our new dependency `get_current_user` will receive a `token` as a `str` from the sub-dependency `oauth2_scheme`:

```Python
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)]):
    user = fake_decode_token(token)
    return user
```

###  Get the user (`fake_decode_token`)

`get_current_user` will use a (fake) utility function we created, that takes a token as a `str` and returns our Pydantic `User` model:

```Python
def fake_decode_token(token):
    return User(
        username=token + "fakedecoded", email="john@example.com", full_name="John Doe"
    )
```


### Inject the current user

So now we can use the same `Depends` with our `get_current_user` in the _path operation_:

```Python
from typing import Union

from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel
from typing_extensions import Annotated

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
    username: str
    email: Union[str, None] = None
    full_name: Union[str, None] = None
    disabled: Union[bool, None] = None


def fake_decode_token(token):
    return User(
        username=token + "fakedecoded", email="john@example.com", full_name="John Doe"
    )


async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)]):
    user = fake_decode_token(token)
    return user


@app.get("/users/me")
async def read_users_me(current_user: Annotated[User, Depends(get_current_user)]):
    return current_user
```

Notice that we declare the type of `current_user` as the Pydantic model `User`.

This will help us inside of the function with all the completion and type checks.

## 40. Реализуйте простой OAuth2 с Password и Bearer


### Get the `username` and `password`

We are going to use **FastAPI** security utilities to get the `username` and `password`.

OAuth2 specifies that when using the "password flow" (that we are using) the client/user must send a `username` and `password` fields as form data.

And the spec says that the fields have to be named like that. So `user-name` or `email` wouldn't work.

But don't worry, you can show it as you wish to your final users in the frontend.

And your database models can use any other names you want.

But for the login _path operation_, we need to use these names to be compatible with the spec (and be able to, for example, use the integrated API documentation system).

The spec also states that the `username` and `password` must be sent as form data (so, no JSON here).

#### `scope`

The spec also says that the client can send another form field "`scope`".

The form field name is `scope` (in singular), but it is actually a long string with "scopes" separated by spaces.

Each "scope" is just a string (without spaces).

They are normally used to declare specific security permissions, for example:

-   `users:read` or `users:write` are common examples.
-   `instagram_basic` is used by Facebook / Instagram.
-   `https://www.googleapis.com/auth/drive` is used by Google.

> **Info**:
> In OAuth2 a "scope" is just a string that declares a specific permission required
> 
> It doesn't matter if it has other characters like `:` or if it is a URL
> 
> Those details are implementation specific.
> 
> For OAuth2 they are just strings.

### Code to get the username and password

Now let's use the utilities provided by **FastAPI** to handle this.

#### `OAuth2PasswordRequestForm`

First, import `OAuth2PasswordRequestForm`, and use it as a dependency with `Depends` in the _path operation_ for `/token`:

```Python
from typing import Annotated

from fastapi import Depends, FastAPI, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from pydantic import BaseModel

fake_users_db = {
    "johndoe": {
        "username": "johndoe",
        "full_name": "John Doe",
        "email": "johndoe@example.com",
        "hashed_password": "fakehashedsecret",
        "disabled": False,
    },
    "alice": {
        "username": "alice",
        "full_name": "Alice Wonderson",
        "email": "alice@example.com",
        "hashed_password": "fakehashedsecret2",
        "disabled": True,
    },
}

app = FastAPI()


def fake_hash_password(password: str):
    return "fakehashed" + password


oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


class User(BaseModel):
    username: str
    email: str | None = None
    full_name: str | None = None
    disabled: bool | None = None


class UserInDB(User):
    hashed_password: str


def get_user(db, username: str):
    if username in db:
        user_dict = db[username]
        return UserInDB(**user_dict)


def fake_decode_token(token):
    # This doesn't provide any security at all
    # Check the next version
    user = get_user(fake_users_db, token)
    return user


async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)]):
    user = fake_decode_token(token)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials",
            headers={"WWW-Authenticate": "Bearer"},
        )
    return user


async def get_current_active_user(
    current_user: Annotated[User, Depends(get_current_user)]
):
    if current_user.disabled:
        raise HTTPException(status_code=400, detail="Inactive user")
    return current_user


@app.post("/token")
async def login(form_data: Annotated[OAuth2PasswordRequestForm, Depends()]):
    user_dict = fake_users_db.get(form_data.username)
    if not user_dict:
        raise HTTPException(status_code=400, detail="Incorrect username or password")
    user = UserInDB(**user_dict)
    hashed_password = fake_hash_password(form_data.password)
    if not hashed_password == user.hashed_password:
        raise HTTPException(status_code=400, detail="Incorrect username or password")

    return {"access_token": user.username, "token_type": "bearer"}


@app.get("/users/me")
async def read_users_me(
    current_user: Annotated[User, Depends(get_current_active_user)]
):
    return current_user
```


`OAuth2PasswordRequestForm` is a class dependency that declares a form body with:

-   The `username`.
-   The `password`.
-   An optional `scope` field as a big string, composed of strings separated by spaces.
-   An optional `grant_type`.

> **Tip**:
> The OAuth2 spec actually _requires_ a field `grant_type` with a fixed value of `password`, but `OAuth2PasswordRequestForm` doesn't enforce it.
> 
> If you need to enforce it, use `OAuth2PasswordRequestFormStrict` instead of `OAuth2PasswordRequestForm`.


-   An optional `client_id` (we don't need it for our example).
-   An optional `client_secret` (we don't need it for our example).

> **Info**:
> The `OAuth2PasswordRequestForm` is not a special class for **FastAPI** as is `OAuth2PasswordBearer`.
> 
> `OAuth2PasswordBearer` makes **FastAPI** know that it is a security scheme. So it is added that way to OpenAPI.
> 
> But `OAuth2PasswordRequestForm` is just a class dependency that you could have written yourself, or you could have declared `Form` parameters directly.

But as it's a common use case, it is provided by **FastAPI** directly, just to make it easier.

### See it in action

![[Pasted image 20230405202159.png]]

![[Pasted image 20230405202207.png]]


#### Get your own user data

Now use the operation `[[HTTP METHOD GET|GET]]` with the path `/users/me`.

You will get your user's data, like:

```json
{
  "username": "johndoe",
  "email": "johndoe@example.com",
  "full_name": "John Doe",
  "disabled": false,
  "hashed_password": "fakehashedsecret"
}
```

![[Pasted image 20230405202307.png]]

![[Pasted image 20230405202317.png]]

## 41. Реализуйте OAuth2 с Password (and hashing), Bearer with JWT tokens

Now that we have all the security flow, let's make the application actually secure, using JWT tokens and secure password hashing.

This code is something you can actually use in your application, save the password hashes in your database, etc.

We are going to start from where we left in the previous chapter and increment it.

### About JWT

JWT means "**JSON Web Tokens**".

It's a standard to codify a JSON object in a long dense string without spaces. It looks like this:

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c`

It is not encrypted, so, anyone could recover the information from the contents.s

But it's signed. So, when you receive a token that you emitted, you can verify that you actually emitted it.

That way, you can create a token with an expiration of, let's say, 1 week. And then when the user comes back the next day with the token, you know that user is still logged in to your system.

After a week, the token will be expired and the user will not be authorized and will have to sign in again to get a new token. And if the user (or a third party) tried to modify the token to change the expiration, you would be able to discover it, because the signatures would not match.

### Install `python-jose`

We need to install `python-jose` to generate and verify the JWT tokens in Python:

![[Pasted image 20230405202807.png]]

[Python-jose](https://github.com/mpdavis/python-jose) requires a cryptographic backend as an extra.

Here we are using the recommended one: [pyca/cryptography](https://cryptography.io/).

### Password hashing

"Hashing" means converting some content (a password in this case) into a sequence of bytes (just a string) that looks like gibberish.

Whenever you pass exactly the same content (exactly the same password) you get exactly the same gibberish.

But you cannot convert from the gibberish back to the password.

### Why use password hashing

If your database is stolen, the thief won't have your users' plaintext passwords, only the hashes.

So, the thief won't be able to try to use that password in another system (as many users use the same password everywhere, this would be dangerous).

### Install `passlib`

PassLib is a great Python package to handle password hashes.

It supports many secure hashing algorithms and utilities to work with them.

The recommended algorithm is "Bcrypt".

So, install PassLib with Bcrypt:

![[Pasted image 20230405202938.png]]

### Hash and verify the passwords

Import the tools we need from `passlib`.

Create a PassLib "context". This is what will be used to hash and verify passwords

> **Tip**:
> The PassLib context also has functionality to use different hashing algorithms, including deprecated old ones only to allow verifying them, etc
> 
> For example, you could use it to read and verify passwords generated by another system (like Django) but hash any new passwords with a different algorithm like Bcrypt
> 
> And be compatible with all of them at the same time.

Create a utility function to hash a password coming from the user.

And another utility to verify if a received password matches the hash stored.

And another one to authenticate and return a user.

```Python
from datetime import datetime, timedelta
from typing import Annotated

from fastapi import Depends, FastAPI, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from jose import JWTError, jwt
from passlib.context import CryptContext
from pydantic import BaseModel

# to get a string like this run:
# openssl rand -hex 32
SECRET_KEY = "09d25e094faa6ca2556c818166b7a9563b93f7099f6f0f4caa6cf63b88e8d3e7"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30


fake_users_db = {
    "johndoe": {
        "username": "johndoe",
        "full_name": "John Doe",
        "email": "johndoe@example.com",
        "hashed_password": "$2b$12$EixZaYVK1fsbw1ZfbX3OXePaWxn96p36WQoeG6Lruj3vjPGga31lW",
        "disabled": False,
    }
}


class Token(BaseModel):
    access_token: str
    token_type: str


class TokenData(BaseModel):
    username: str | None = None


class User(BaseModel):
    username: str
    email: str | None = None
    full_name: str | None = None
    disabled: bool | None = None


class UserInDB(User):
    hashed_password: str


pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

app = FastAPI()


def verify_password(plain_password, hashed_password):
    return pwd_context.verify(plain_password, hashed_password)


def get_password_hash(password):
    return pwd_context.hash(password)


def get_user(db, username: str):
    if username in db:
        user_dict = db[username]
        return UserInDB(**user_dict)


def authenticate_user(fake_db, username: str, password: str):
    user = get_user(fake_db, username)
    if not user:
        return False
    if not verify_password(password, user.hashed_password):
        return False
    return user


def create_access_token(data: dict, expires_delta: timedelta | None = None):
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=15)
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt


async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)]):
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception
        token_data = TokenData(username=username)
    except JWTError:
        raise credentials_exception
    user = get_user(fake_users_db, username=token_data.username)
    if user is None:
        raise credentials_exception
    return user


async def get_current_active_user(
    current_user: Annotated[User, Depends(get_current_user)]
):
    if current_user.disabled:
        raise HTTPException(status_code=400, detail="Inactive user")
    return current_user


@app.post("/token", response_model=Token)
async def login_for_access_token(
    form_data: Annotated[OAuth2PasswordRequestForm, Depends()]
):
    user = authenticate_user(fake_users_db, form_data.username, form_data.password)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
            headers={"WWW-Authenticate": "Bearer"},
        )
    access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    access_token = create_access_token(
        data={"sub": user.username}, expires_delta=access_token_expires
    )
    return {"access_token": access_token, "token_type": "bearer"}


@app.get("/users/me/", response_model=User)
async def read_users_me(
    current_user: Annotated[User, Depends(get_current_active_user)]
):
    return current_user


@app.get("/users/me/items/")
async def read_own_items(
    current_user: Annotated[User, Depends(get_current_active_user)]
):
    return [{"item_id": "Foo", "owner": current_user.username}]
```

### Technical details about the JWT "subject" `sub`

The JWT specification says that there's a key `sub`, with the subject of the token.

It's optional to use it, but that's where you would put the user's identification, so we are using it here.

JWT might be used for other things apart from identifying a user and allowing them to perform operations directly on your API.

For example, you could identify a "car" or a "blog post".

Then you could add permissions about that entity, like "drive" (for the car) or "edit" (for the blog).

And then, you could give that JWT token to a user (or bot), and they could use it to perform those actions (drive the car, or edit the blog post) without even needing to have an account, just with the JWT token your API generated for that.

Using these ideas, JWT can be used for way more sophisticated scenarios.

In those cases, several of those entities could have the same ID, let's say `foo` (a user `foo`, a car `foo`, and a blog post `foo`).

So, to avoid ID collisions, when creating the JWT token for the user, you could prefix the value of the `sub` key, e.g. with `username:`. So, in this example, the value of `sub` could have been: `username:johndoe`.

The important thing to have in mind is that the `sub` key should have a unique identifier across the entire application, and it should be a string.

### Check it

![[Pasted image 20230405204913.png]]

Authorize the application the same way as before.

Using the credentials:

Username: `johndoe` Password: `secret`

![[Pasted image 20230405204923.png]]

Call the endpoint `/users/me/`, you will get the response as:

```json
{
  "username": "johndoe",
  "email": "johndoe@example.com",
  "full_name": "John Doe",
  "disabled": false
}
```

![[Pasted image 20230405204942.png]]

If you open the developer tools, you could see how the data sent only includes the token, the password is only sent in the first request to authenticate the user and get that access token, but not afterwards:

![[Pasted image 20230405204952.png]]

## Advanced usage with `scopes`[¶](https://fastapi.tiangolo.com/tutorial/security/oauth2-jwt/#advanced-usage-with-scopes "Permanent link")

OAuth2 has the notion of "scopes".

You can use them to add a specific set of permissions to a JWT token.

Then you can give this token to a user directly or a third party, to interact with your API with a set of restrictions.

You can learn how to use them and how they are integrated into **FastAPI** later in the **Advanced User Guide**.