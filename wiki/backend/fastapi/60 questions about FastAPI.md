---
created: [" 02-04-2023 20:49"]
aliases: []
tags:
- article/
---

# 60 questions about FastAPI

This article about tech interview questions on topic `FastAPI`

## List

1. Что такое FastAPI?

	Это современный асинхронный веб-фреймворк для построения API на базе Python 3.7+, основный на starlette. Одной из его фишек является type hints, которые используются для проверки типов объектов и возвращаемых функциями значений

2. Расскажите о том, как построен FastAPI, о его архитектуре

	FastAPI построен на базе Starlette (это облегченный фреймворк [[ASGI]] / тулкит)
	
	FastAPI добавляет к Starlette дополнительные функции, такие как автоматическая проверка данных, сериализация и документация.

3. Какие автодокументации FastAPI поддерживает изначально?
	
	* SwaggerUI
	* OpenAPI

4. Какие преимущества FastAPI перед другими фреймворками? Например Django и Flask

	Перед Django:
	* FastAPI - библеотка, а не фреймворк. Это дает разработчикам гибкость при создании архитектуры и планирования приложения. Но также и стоит отметить, что это несет дополнительные риски, т.к. если разработчик неопытен - он может построить плохую архитектуру, которую сложно поддерживать в будущем
	* Асинхронный. Эта возможность дает дополнительную скорость обработки запросов, Django - тоже асинхронный, но только для ORM и с версии 4, DRF по прежнему является полностью синхронным, к тому же разрабатывать RestAPI на Django можно только при помощи DRF
	* Не несет в себе "комплект батареек" - все, что необходимо, подключаем сами. Это помогает облегчить вес итогового продукта
	* Django официально не поддерживает NoSQL базы данных. Их использование в нем не рекомендуется
	
	Перед Flask:
	1. Flask - [[WSGI]], т.е. только синхронное RestAPI
	1. Flask изначально не поддерживает проверку типов данных
	2. Flask изначально не поддерживает аутентификации 
	3. Flask изначально не поддерживает авто-документацию (Fastapi в то же время поддерживает авто-документацию изначально - SwaggerUI/OpenAPI)
	
	5. Что такое uvicorn? Какие преимущества и недостатки?
	
	Это веб-сервер, разработанный на питоне и поддерживающий протокол [[ASGI]].
	
	Как преимущество - поддержка WebSockets
	Как недостаток выделю - поддержку только HTTP/1.1
	
5. Можете ли вы сказать мне, что такое automatic validation? Как это работает? 
	
	Автоматическая проверка — это одна из фичей FastAPI, которая автоматически проверяет входящие запросы на соответствие определенной схеме. Это гарантирует, что обрабатываются только действительные запросы, и помогает предотвратить злонамеренный ввод, который может привести к ошибкам или неожиданному поведению.

	Обычно это достигается посредством Pydantic.

6. Как создать основное приложение FastAPI?
	```Python
	from fastapi import FastAPI
	
	my_awesome_api = FastAPI() 
	
	@my_awesome_api.get("/") 
	async def root(): 
		return {"message": "Hello World"}
	```
	
	And put it in a file `main.py`, then you would call `uvicorn` like:
	
	![[Pasted image 20230402213827.png]]

7. Расскажите про основные HTTP методы:

	* [[HTTP METHOD GET|GET]]
	* [[HTTP METHOD POST|POST]]
	* [[HTTP METHOD PUT|PUT]]
	* [[HTTP METHOD DELETE|DELETE]]
	
	Более экзотические:
	
	* [[HTTP METHOD OPTIONS|OPTIONS]]
	* [[HTTP METHOD HEAD|HEAD]]
	* [[HTTP METHOD PATCH|PATCH]]
	* [[HTTP METHOD TRACE|TRACE]]

1. Как добавить самый простой ендпоинт в FastAPI?
	
	```Python
	from fastapi import FastAPI
	
	app = FastAPI()
	
	
	@app.get("/")
	async def root():
	    return {"message": "Hello World"}
	```

1. Как добавить ендпоинт в FastAPI с динамическим путем? (т.е. на конце ендпоинта например у нас динамическкая переменная, зависящая например от id чего-нибудь)

	```Python
	from fastapi import FastAPI
	
	app = FastAPI()
	
	
	@app.get("/items/{item_id}")
	async def read_item(item_id: int):
	    return {"item_id": item_id}
	```

1. Что такое pydantic? зачем он нужен?

	Pydantic -  это библиотека для проверки данных и управление настройками с использованием аннотаций типа Python.
	
	Вся проверка данных выполняется Pydantic «под капотом» FastAPI. 

10. Предположим мы хотим иметь ендпоинт и с path-parameter, но хотим задать ограничения на такие параметры. Приведите способ это сделать
	
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

11. А если хотим тоже самое, но указать как path parameter - путь до файла?
	
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

12. Как создать ендпоинт с определенными полями запроса (Query Parameters)?

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

13. Как создать ендпоинт с multiple path and query parameters в FastAPI?

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

14. Как создать ендпоинт с определенным телом запроса? (т.е. запрос должен содержать определенные поля)

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

15. Как создать ендпоинт с body + path + query parameters 

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

16. Расскажите о `Query` в FastAPI и почему он полезен как query parameter и string validation?

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

17. Расскажите о `Path` в FastAPI и почему он полезен как path parameter и numeric validation?

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

17. Расскажите о `Body` в FastAPI

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

18. Продолжить с Body Fields
https://fastapi.tiangolo.com/tutorial/body-fields/