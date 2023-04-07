---
created: [" 06-04-2023 10:22"]
aliases: []
tags:
- article/
---

# 60 questions about FastAPI (40-60)


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

### Advanced usage with `scopes`[¶](https://fastapi.tiangolo.com/tutorial/security/oauth2-jwt/#advanced-usage-with-scopes "Permanent link")

OAuth2 has the notion of "scopes".

You can use them to add a specific set of permissions to a JWT token.

Then you can give this token to a user directly or a third party, to interact with your API with a set of restrictions.

You can learn how to use them and how they are integrated into **FastAPI** later in the **Advanced User Guide**.

## 42. Расскажите о Middleware в FastAPI

A "middleware" is a function that works with every **request** before it is processed by any specific _path operation_. And also with every **response** before returning it.

-   It takes each **request** that comes to your application.
-   It can then do something to that **request** or run any needed code.
-   Then it passes the **request** to be processed by the rest of the application (by some _path operation_).
-   It then takes the **response** generated by the application (by some _path operation_).
-   It can do something to that **response** or run any needed code.
-   Then it returns the **response**.

### Create a middleware

To create a middleware you use the decorator `@app.middleware("http")` on top of a function.

The middleware function receives:

-   The `request`.
-   A function `call_next` that will receive the `request` as a parameter.
    -   This function will pass the `request` to the corresponding _path operation_.
    -   Then it returns the `response` generated by the corresponding _path operation_.
-   You can then modify further the `response` before returning it.

```Python
@app.middleware("http")
async def add_process_time_header(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    process_time = time.time() - start_time
    response.headers["X-Process-Time"] = str(process_time)
    return response
```

## 43. Что такое CORS? Как установить корсы в FastAPI?

[CORS or "Cross-Origin Resource Sharing"](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) refers to the situations when a frontend running in a browser has JavaScript code that communicates with a backend, and the backend is in a different "origin" than the frontend.

### Origin

An origin is the combination of protocol (`http`, `https`), domain (`myapp.com`, `localhost`, `localhost.tiangolo.com`), and port (`80`, `443`, `8080`).

So, all these are different origins:

-   `http://localhost`
-   `https://localhost`
-   `http://localhost:8080`

Even if they are all in `localhost`, they use different protocols or ports, so, they are different "origins".

### Steps

So, let's say you have a frontend running in your browser at `http://localhost:8080`, and its JavaScript is trying to communicate with a backend running at `http://localhost` (because we don't specify a port, the browser will assume the default port `80`).

Then, the browser will send an [[HTTP METHOD OPTIONS|HTTP OPTIONS]] request to the backend, and if the backend sends the appropriate headers authorizing the communication from this different origin (`http://localhost:8080`) then the browser will let the JavaScript in the frontend send its request to the backend.

To achieve this, the backend must have a list of "allowed origins".

In this case, it would have to include `http://localhost:8080` for the frontend to work correctly.

### Wildcards

It's also possible to declare the list as `"*"` (a "wildcard") to say that all are allowed.

But that will only allow certain types of communication, excluding everything that involves credentials: Cookies, Authorization headers like those used with Bearer Tokens, etc.

So, for everything to work correctly, it's better to specify explicitly the allowed origins.

### Use `CORSMiddleware`

You can configure it in your **FastAPI** application using the `CORSMiddleware`.

-   Import `CORSMiddleware`.
-   Create a list of allowed origins (as strings).
-   Add it as a "middleware" to your **FastAPI** application.

You can also specify if your backend allows:

-   Credentials (Authorization headers, Cookies, etc).
-   Specific HTTP methods (`POST`, `PUT`) or all of them with the wildcard `"*"`.
-   Specific HTTP headers or all of them with the wildcard `"*"`.

```Python
origins = [
    "http://localhost.tiangolo.com",
    "https://localhost.tiangolo.com",
    "http://localhost",
    "http://localhost:8080",
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/")
async def main():
    return {"message": "Hello World"}
```

The default parameters used by the `CORSMiddleware` implementation are restrictive by default, so you'll need to explicitly enable particular origins, methods, or headers, in order for browsers to be permitted to use them in a Cross-Domain context.

The following arguments are supported:

-   `allow_origins` - A list of origins that should be permitted to make cross-origin requests. E.g. `['https://example.org', 'https://www.example.org']`. You can use `['*']` to allow any origin.
-   `allow_origin_regex` - A regex string to match against origins that should be permitted to make cross-origin requests. e.g. `'https://.*\.example\.org'`.
-   `allow_methods` - A list of HTTP methods that should be allowed for cross-origin requests. Defaults to `['GET']`. You can use `['*']` to allow all standard methods.
-   `allow_headers` - A list of HTTP request headers that should be supported for cross-origin requests. Defaults to `[]`. You can use `['*']` to allow all headers. The `Accept`, `Accept-Language`, `Content-Language` and `Content-Type` headers are always allowed for [simple CORS requests](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#simple_requests).
-   `allow_credentials` - Indicate that cookies should be supported for cross-origin requests. Defaults to `False`. Also, `allow_origins` cannot be set to `['*']` for credentials to be allowed, origins must be specified.
-   `expose_headers` - Indicate any response headers that should be made accessible to the browser. Defaults to `[]`.
-   `max_age` - Sets a maximum time in seconds for browsers to cache CORS responses. Defaults to `600`.

The middleware responds to two particular types of HTTP request...

#### 1. CORS preflight requests

These are any `OPTIONS` request with `Origin` and `Access-Control-Request-Method` headers.

In this case the middleware will intercept the incoming request and respond with appropriate CORS headers, and either a `200` or `400` response for informational purposes.

#### 2. Simple requests

Any request with an `Origin` header. In this case the middleware will pass the request through as normal, but will include appropriate CORS headers on the response.

## 44. Расскажите об APIRouter и роутинге файлов в целом в FastAPI

Let's say you have a file structure like this:

```
.
├── app                  # "app" is a Python package
│   ├── __init__.py      # this file makes "app" a "Python package"
│   ├── main.py          # "main" module, e.g. import app.main
│   ├── dependencies.py  # "dependencies" module, e.g. import app.dependencies
│   └── routers          # "routers" is a "Python subpackage"
│   │   ├── __init__.py  # makes "routers" a "Python subpackage"
│   │   ├── items.py     # "items" submodule, e.g. import app.routers.items
│   │   └── users.py     # "users" submodule, e.g. import app.routers.users
│   └── internal         # "internal" is a "Python subpackage"
│       ├── __init__.py  # makes "internal" a "Python subpackage"
│       └── admin.py     # "admin" submodule, e.g. import app.internal.admin
```

### APIRouter

Let's say the file dedicated to handling just users is the submodule at `/app/routers/users.py`.

You want to have the _path operations_ related to your users separated from the rest of the code, to keep it organized.

But it's still part of the same **FastAPI** application/web API (it's part of the same "Python Package").

You can create the _path operations_ for that module using `APIRouter`.

You import it and create an "instance" the same way you would with the class `FastAPI`:

```Python
from fastapi import APIRouter

router = APIRouter()


@router.get("/users/", tags=["users"])
async def read_users():
    return [{"username": "Rick"}, {"username": "Morty"}]


@router.get("/users/me", tags=["users"])
async def read_user_me():
    return {"username": "fakecurrentuser"}


@router.get("/users/{username}", tags=["users"])
async def read_user(username: str):
    return {"username": username}
```

You can think of `APIRouter` as a "mini `FastAPI`" class.

All the same options are supported.

All the same `parameters`, `responses`, `dependencies`, `tags`, etc.

###  Dependencies and APIRouter

We see that we are going to need some dependencies used in several places of the application.

So we put them in their own `dependencies` module (`app/dependencies.py`).

We will now use a simple dependency to read a custom `X-Token` header:

```Python
from fastapi import Header, HTTPException
from typing_extensions import Annotated


async def get_token_header(x_token: Annotated[str, Header()]):
    if x_token != "fake-super-secret-token":
        raise HTTPException(status_code=400, detail="X-Token header invalid")


async def get_query_token(token: str):
    if token != "jessica":
        raise HTTPException(status_code=400, detail="No Jessica token provided")
```

et's say you also have the endpoints dedicated to handling "items" from your application in the module at `app/routers/items.py`.

You have _path operations_ for:

-   `/items/`
-   `/items/{item_id}`

It's all the same structure as with `app/routers/users.py`.

But we want to be smarter and simplify the code a bit.

We know all the _path operations_ in this module have the same:

-   Path `prefix`: `/items`.
-   `tags`: (just one tag: `items`).
-   Extra `responses`.
-   `dependencies`: they all need that `X-Token` dependency we created.

So, instead of adding all that to each _path operation_, we can add it to the `APIRouter`.

```Python
from fastapi import APIRouter, Depends, HTTPException

from ..dependencies import get_token_header

router = APIRouter(
    prefix="/items",
    tags=["items"],
    dependencies=[Depends(get_token_header)],
    responses={404: {"description": "Not found"}},
)


fake_items_db = {"plumbus": {"name": "Plumbus"}, "gun": {"name": "Portal Gun"}}


@router.get("/")
async def read_items():
    return fake_items_db


@router.get("/{item_id}")
async def read_item(item_id: str):
    if item_id not in fake_items_db:
        raise HTTPException(status_code=404, detail="Item not found")
    return {"name": fake_items_db[item_id]["name"], "item_id": item_id}


@router.put(
    "/{item_id}",
    tags=["custom"],
    responses={403: {"description": "Operation forbidden"}},
)
async def update_item(item_id: str):
    if item_id != "plumbus":
        raise HTTPException(
            status_code=403, detail="You can only update the item: plumbus"
        )
    return {"item_id": item_id, "name": "The great Plumbus"}
```

As the path of each _path operation_ has to start with `/`, like in:

`@router.get("/{item_id}") async def read_item(item_id: str):     ...`

**...the prefix must not include a final `/`.**

So, the prefix in this case is `/items`.

We can also add a list of `tags` and extra `responses` that will be applied to all the _path operations_ included in this router.

And we can add a list of `dependencies` that will be added to all the _path operations_ in the router and will be executed/solved for each request made to them.

> **Tip**:
> Note that, much like dependencies in path operation decorators, no value will be passed to your path operation function.

### Connect all APIRouter's to main app

And we can even declare [global dependencies](https://fastapi.tiangolo.com/tutorial/dependencies/global-dependencies/) that will be combined with the dependencies for each `APIRouter`:

```Python
from fastapi import Depends, FastAPI

from .dependencies import get_query_token, get_token_header
from .internal import admin
from .routers import items, users

app = FastAPI(dependencies=[Depends(get_query_token)])


app.include_router(users.router)
app.include_router(items.router)
app.include_router(
    admin.router,
    prefix="/admin",
    tags=["admin"],
    dependencies=[Depends(get_token_header)],
    responses={418: {"description": "I'm a teapot"}},
)


@app.get("/")
async def root():
    return {"message": "Hello Bigger Applications!"}
```

### Include the same router multiple times with different `prefix`

You can also use `.include_router()` multiple times with the _same_ router using different prefixes.

This could be useful, for example, to expose the same API under different prefixes, e.g. `/api/v1` and `/api/latest`.

This is an advanced usage that you might not really need, but it's there in case you do.

### Include an APIRouter in another

The same way you can include an `APIRouter` in a `FastAPI` application, you can include an `APIRouter` in another `APIRouter` using:

`router.include_router(other_router)`

Make sure you do it before including `router` in the `FastAPI` app, so that the _path operations_ from `other_router` are also included.

## 45. Расскажите о FastAPI и взаимодействии с БД. Реализуйте коннект

### ORMs

**FastAPI** works with any database and any style of library to talk to the database.

A common pattern is to use an "ORM": an "object-relational mapping" library.

An ORM has tools to convert ("_map_") between _objects_ in code and database tables ("_relations_").

With an ORM, you normally create a class that represents a table in a SQL database, each attribute of the class represents a column, with a name and a type.

For example a class `Pet` could represent a SQL table `pets`.

And each _instance_ object of that class represents a row in the database.

For example an object `orion_cat` (an instance of `Pet`) could have an attribute `orion_cat.type`, for the column `type`. And the value of that attribute could be, e.g. `"cat"`.

These ORMs also have tools to make the connections or relations between tables or entities.

This way, you could also have an attribute `orion_cat.owner` and the owner would contain the data for this pet's owner, taken from the table _owners_.

So, `orion_cat.owner.name` could be the name (from the `name` column in the `owners` table) of this pet's owner.

It could have a value like `"Arquilian"`.

And the ORM will do all the work to get the information from the corresponding table _owners_ when you try to access it from your pet object.

Common ORMs are for example: Django-ORM (part of the Django framework), SQLAlchemy ORM (part of SQLAlchemy, independent of framework) and Peewee (independent of framework), among others.

Here we will see how to work with **SQLAlchemy ORM**.

In a similar way you could use any other ORM.

### File structure

For these examples, let's say you have a directory named `my_super_project` that contains a sub-directory called `sql_app` with a structure like this:

```
.
└── sql_app
    ├── __init__.py
    ├── crud.py
    ├── database.py
    ├── main.py
    ├── models.py
    └── schemas.py
```

The file `__init__.py` is just an empty file, but it tells Python that `sql_app` with all its modules (Python files) is a package.

Now let's see what each file/module does.

### Import the SQLAlchemy parts

```Python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "sqlite:///./sql_app.db"
# SQLALCHEMY_DATABASE_URL = "postgresql://user:password@postgresserver/db"

engine = create_engine(
    SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}
)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```

#### Create a database URL for SQLAlchemy

In this example, we are "connecting" to a SQLite database (opening a file with the SQLite database).

The file will be located at the same directory in the file `sql_app.db`.

That's why the last part is `./sql_app.db`.

If you were using a **PostgreSQL** database instead, you would just have to uncomment the line:

`SQLALCHEMY_DATABASE_URL = "postgresql://user:password@postgresserver/db"`

...and adapt it with your database data and credentials (equivalently for MySQL, MariaDB or any other).

#### Create a `SessionLocal` class

Each instance of the `SessionLocal` class will be a database session. The class itself is not a database session yet.

But once we create an instance of the `SessionLocal` class, this instance will be the actual database session.

We name it `SessionLocal` to distinguish it from the `Session` we are importing from SQLAlchemy.

We will use `Session` (the one imported from SQLAlchemy) later.

To create the `SessionLocal` class, use the function `sessionmaker`:

#### Create a Base class

Now we will use the function `declarative_base()` that returns a class.

Later we will inherit from this class to create each of the database models or classes (the ORM models):

### Create SQLAlchemy models from the Base class

Import `Base` from `database` (the file `database.py` from above).

Create classes that inherit from it.

These classes are the SQLAlchemy models.

```Python
from sqlalchemy import Boolean, Column, ForeignKey, Integer, String
from sqlalchemy.orm import relationship

from .database import Base


class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True, index=True)
    hashed_password = Column(String)
    is_active = Column(Boolean, default=True)

    items = relationship("Item", back_populates="owner")


class Item(Base):
    __tablename__ = "items"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, index=True)
    description = Column(String, index=True)
    owner_id = Column(Integer, ForeignKey("users.id"))

    owner = relationship("User", back_populates="items")
```

The `__tablename__` attribute tells SQLAlchemy the name of the table to use in the database for each of these models.

#### Create model attributes/columns

Now create all the model (class) attributes.

Each of these attributes represents a column in its corresponding database table.

We use `Column` from SQLAlchemy as the default value.

And we pass a SQLAlchemy class "type", as `Integer`, `String`, and `Boolean`, that defines the type in the database, as an argument.

#### Create the relationships

Now create the relationships.

For this, we use `relationship` provided by SQLAlchemy ORM.

This will become, more or less, a "magic" attribute that will contain the values from other tables related to this one.


When accessing the attribute `items` in a `User`, as in `my_user.items`, it will have a list of `Item` SQLAlchemy models (from the `items` table) that have a foreign key pointing to this record in the `users` table.

When you access `my_user.items`, SQLAlchemy will actually go and fetch the items from the database in the `items` table and populate them here.

And when accessing the attribute `owner` in an `Item`, it will contain a `User` SQLAlchemy model from the `users` table. It will use the `owner_id` attribute/column with its foreign key to know which record to get from the `users` table.

### Create the Pydantic models

Now let's check the file `sql_app/schemas.py`.

> **Tip**:
>
> To avoid confusion between the SQLAlchemy _models_ and the Pydantic _models_, we will have the file `models.py` with the SQLAlchemy models, and the file `schemas.py` with the Pydantic models.
> 
> These Pydantic models define more or less a "schema" (a valid data shape).
> 
> So this will help us avoiding confusion while using both.

#### Create initial Pydantic models / schemas

Create an `ItemBase` and `UserBase` Pydantic _models_ (or let's say "schemas") to have common attributes while creating or reading data.

And create an `ItemCreate` and `UserCreate` that inherit from them (so they will have the same attributes), plus any additional data (attributes) needed for creation.

So, the user will also have a `password` when creating it.

But for security, the `password` won't be in other Pydantic _models_, for example, it won't be sent from the API when reading a user.

```Python
from pydantic import BaseModel


class ItemBase(BaseModel):
    title: str
    description: str | None = None


class ItemCreate(ItemBase):
    pass


class Item(ItemBase):
    id: int
    owner_id: int

    class Config:
        orm_mode = True


class UserBase(BaseModel):
    email: str


class UserCreate(UserBase):
    password: str


class User(UserBase):
    id: int
    is_active: bool
    items: list[Item] = []

    class Config:
        orm_mode = True
```

##### SQLAlchemy style and Pydantic style

Notice that SQLAlchemy _models_ define attributes using `=`, and pass the type as a parameter to `Column`, like in:

```Python
name = Column(String)
```

while Pydantic _models_ declare the types using `:`, the new type annotation syntax/type hints:

```Python
name: str
```

Have it in mind, so you don't get confused when using `=` and `:` with them.

#### Create Pydantic models / schemas for reading / returning

Now create Pydantic _models_ (schemas) that will be used when reading data, when returning it from the API.

For example, before creating an item, we don't know what will be the ID assigned to it, but when reading it (when returning it from the API) we will already know its ID.

The same way, when reading a user, we can now declare that `items` will contain the items that belong to this user.

Not only the IDs of those items, but all the data that we defined in the Pydantic _model_ for reading items: `Item`

#### Use Pydantic's orm_mode

Now, in the Pydantic _models_ for reading, `Item` and `User`, add an internal `Config` class.

This [`Config`](https://pydantic-docs.helpmanual.io/usage/model_config/) class is used to provide configurations to Pydantic.

In the `Config` class, set the attribute `orm_mode = True`.

> **Tip**:
> 
> Notice it's assigning a value with `=`, like:
> 
> `orm_mode = True`
> 
> It doesn't use `:` as for the type declarations before.
> 
> This is setting a config value, not declaring a type.

Pydantic's `orm_mode` will tell the Pydantic _model_ to read the data even if it is not a `dict`, but an ORM model (or any other arbitrary object with attributes).

This way, instead of only trying to get the `id` value from a `dict`, as in:

```Python
id = data["id"]
```

it will also try to get it from an attribute, as in:

```Python
id = data.id
```

And with this, the Pydantic _model_ is compatible with ORMs, and you can just declare it in the `response_model` argument in your _path operations_.

You will be able to return a database model and it will read the data from it.

#### **Technical Details about ORM mode**

SQLAlchemy and many others are by default "lazy loading".

That means, for example, that they don't fetch the data for relationships from the database unless you try to access the attribute that would contain that data.

For example, accessing the attribute `items`:

`current_user.items`

would make SQLAlchemy go to the `items` table and get the items for this user, but not before.

Without `orm_mode`, if you returned a SQLAlchemy model from your _path operation_, it wouldn't include the relationship data.

Even if you declared those relationships in your Pydantic models.

But with ORM mode, as Pydantic itself will try to access the data it needs from attributes (instead of assuming a `dict`), you can declare the specific data you want to return and it will be able to go and get it, even from ORMs.

### Create CRUD utils

Now let's see the file `sql_app/crud.py`.

In this file we will have reusable functions to interact with the data in the database.

**CRUD** comes from: **C**reate, **R**ead, **U**pdate, and **D**elete.

...although in this example we are only creating and reading.

#### Read data

Import `Session` from `sqlalchemy.orm`, this will allow you to declare the type of the `db` parameters and have better type checks and completion in your functions.

Import `models` (the SQLAlchemy models) and `schemas` (the Pydantic _models_ / schemas).

Create utility functions to:

-   Read a single user by ID and by email.
-   Read multiple users.
-   Read multiple items.

```Python
from sqlalchemy.orm import Session

from . import models, schemas


def get_user(db: Session, user_id: int):
    return db.query(models.User).filter(models.User.id == user_id).first()


def get_user_by_email(db: Session, email: str):
    return db.query(models.User).filter(models.User.email == email).first()


def get_users(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.User).offset(skip).limit(limit).all()

def get_items(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.Item).offset(skip).limit(limit).all()
```

> **Tip**:
>
> By creating functions that are only dedicated to interacting with the database (get a user or an item) independent of your path operation function, you can more easily reuse them in multiple parts and also add unit tests for them.

#### Create data

Now create utility functions to create data.

The steps are:

-   Create a SQLAlchemy model _instance_ with your data.
-   `add` that instance object to your database session.
-   `commit` the changes to the database (so that they are saved).
-   `refresh` your instance (so that it contains any new data from the database, like the generated ID).

```Python
from sqlalchemy.orm import Session

from . import models, schemas

def create_user(db: Session, user: schemas.UserCreate):
    fake_hashed_password = user.password + "notreallyhashed"
    db_user = models.User(email=user.email, hashed_password=fake_hashed_password)
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user

def create_user_item(db: Session, item: schemas.ItemCreate, user_id: int):
    db_item = models.Item(**item.dict(), owner_id=user_id)
    db.add(db_item)
    db.commit()
    db.refresh(db_item)
    return db_item
```

> **Tip**:
>
> The SQLAlchemy model for `User` contains a `hashed_password` that should contain a secure hashed version of the password.
>
> But as what the API client provides is the original password, you need to extract it and generate the hashed password in your application.
> 
> And then pass the `hashed_password` argument with the value to save.


> **Tip**:
> Instead of passing each of the keyword arguments to `Item` and reading each one of them from the Pydantic _model_, we are generating a `dict` with the Pydantic _model_'s data with:
>
> `item.dict()`
>
> and then we are passing the `dict`'s key-value pairs as the keyword arguments to the SQLAlchemy `Item`, with:
>
> `Item(**item.dict())`
>
> And then we pass the extra keyword argument `owner_id` that is not provided by the Pydantic _model_, with:
> 
> `Item(**item.dict(), owner_id=user_id)`

### Main FastAPI app

And now in the file `sql_app/main.py` let's integrate and use all the other parts we created before.

#### Create the database tables

In a very simplistic way create the database tables:

```Python
from typing import List

from fastapi import Depends, FastAPI, HTTPException
from sqlalchemy.orm import Session

from . import crud, models, schemas
from .database import SessionLocal, engine

models.Base.metadata.create_all(bind=engine)

app = FastAPI()

...
...
...
```

#### Alembic Note

Normally you would probably initialize your database (create tables, etc) with [Alembic](https://alembic.sqlalchemy.org/en/latest/).

And you would also use Alembic for "migrations" (that's its main job).

A "migration" is the set of steps needed whenever you change the structure of your SQLAlchemy models, add a new attribute, etc. to replicate those changes in the database, add a new column, a new table, etc.

You can find an example of Alembic in a FastAPI project in the templates from [Project Generation - Template](https://fastapi.tiangolo.com/project-generation/). Specifically in [the `alembic` directory in the source code](https://github.com/tiangolo/full-stack-fastapi-postgresql/tree/master/%7B%7Bcookiecutter.project_slug%7D%7D/backend/app/alembic/).

#### Create a dependency

Now use the `SessionLocal` class we created in the `sql_app/database.py` file to create a dependency.

We need to have an independent database session/connection (`SessionLocal`) per request, use the same session through all the request and then close it after the request is finished.

And then a new session will be created for the next request.

For that, we will create a new dependency with `yield`, as explained before in the section about [Dependencies with `yield`](https://fastapi.tiangolo.com/tutorial/dependencies/dependencies-with-yield/).

Our dependency will create a new SQLAlchemy `SessionLocal` that will be used in a single request, and then close it once the request is finished.

```Python
from typing import List

from fastapi import Depends, FastAPI, HTTPException
from sqlalchemy.orm import Session

from . import crud, models, schemas
from .database import SessionLocal, engine

models.Base.metadata.create_all(bind=engine)

app = FastAPI()

# Dependency
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

And then, when using the dependency in a _path operation function_, we declare it with the type `Session` we imported directly from SQLAlchemy.

This will then give us better editor support inside the _path operation function_, because the editor will know that the `db` parameter is of type `Session`:

```Python
from typing import List

from fastapi import Depends, FastAPI, HTTPException
from sqlalchemy.orm import Session

from . import crud, models, schemas
from .database import SessionLocal, engine

models.Base.metadata.create_all(bind=engine)

app = FastAPI()

# Dependency
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.post("/users/", response_model=schemas.User)
def create_user(user: schemas.UserCreate, db: Session = Depends(get_db)):
    db_user = crud.get_user_by_email(db, email=user.email)
    if db_user:
        raise HTTPException(status_code=400, detail="Email already registered")
    return crud.create_user(db=db, user=user)


@app.get("/users/", response_model=List[schemas.User])
def read_users(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    users = crud.get_users(db, skip=skip, limit=limit)
    return users


@app.get("/users/{user_id}", response_model=schemas.User)
def read_user(user_id: int, db: Session = Depends(get_db)):
    db_user = crud.get_user(db, user_id=user_id)
    if db_user is None:
        raise HTTPException(status_code=404, detail="User not found")
    return db_user


@app.post("/users/{user_id}/items/", response_model=schemas.Item)
def create_item_for_user(
    user_id: int, item: schemas.ItemCreate, db: Session = Depends(get_db)
):
    return crud.create_user_item(db=db, item=item, user_id=user_id)


@app.get("/items/", response_model=List[schemas.Item])
def read_items(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    items = crud.get_items(db, skip=skip, limit=limit)
    return items
```

> **Technical Details**:
> 
> The parameter `db` is actually of type `SessionLocal`, but this class (created with `sessionmaker()`) is a "proxy" of a SQLAlchemy `Session`, so, the editor doesn't really know what methods are provided.
> 
> But by declaring the type as `Session`, the editor now can know the available methods (`.add()`, `.query()`, `.commit()`, etc) and can provide better support (like completion). The type declaration doesn't affect the actual object.

### Migrations


Because we are using SQLAlchemy directly and we don't require any kind of plug-in for it to work with **FastAPI**, we could integrate database migrations with [Alembic](https://alembic.sqlalchemy.org/) directly.

And as the code related to SQLAlchemy and the SQLAlchemy models lives in separate independent files, you would even be able to perform the migrations with Alembic without having to install FastAPI, Pydantic, or anything else.

The same way, you would be able to use the same SQLAlchemy models and utilities in other parts of your code that are not related to **FastAPI**.

For example, in a background task worker with [Celery](https://docs.celeryq.dev/), [RQ](https://python-rq.org/), or [ARQ](https://arq-docs.helpmanual.io/).

### Review all the files

Remember you should have a directory named `my_super_project` that contains a sub-directory called `sql_app`.

`sql_app` should have the following files:

-   `sql_app/__init__.py`: is an empty file.
    
-   `sql_app/database.py`:

```Python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "sqlite:///./sql_app.db"
# SQLALCHEMY_DATABASE_URL = "postgresql://user:password@postgresserver/db"

engine = create_engine(
    SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}
)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```

-   `sql_app/models.py`:

```Python
from sqlalchemy import Boolean, Column, ForeignKey, Integer, String
from sqlalchemy.orm import relationship

from .database import Base


class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True, index=True)
    hashed_password = Column(String)
    is_active = Column(Boolean, default=True)

    items = relationship("Item", back_populates="owner")


class Item(Base):
    __tablename__ = "items"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, index=True)
    description = Column(String, index=True)
    owner_id = Column(Integer, ForeignKey("users.id"))

    owner = relationship("User", back_populates="items")
```

-   `sql_app/schemas.py`:

```Python
from pydantic import BaseModel


class ItemBase(BaseModel):
    title: str
    description: str | None = None


class ItemCreate(ItemBase):
    pass


class Item(ItemBase):
    id: int
    owner_id: int

    class Config:
        orm_mode = True


class UserBase(BaseModel):
    email: str


class UserCreate(UserBase):
    password: str


class User(UserBase):
    id: int
    is_active: bool
    items: list[Item] = []

    class Config:
        orm_mode = True
```

-   `sql_app/crud.py`:

```Python
from sqlalchemy.orm import Session

from . import models, schemas


def get_user(db: Session, user_id: int):
    return db.query(models.User).filter(models.User.id == user_id).first()


def get_user_by_email(db: Session, email: str):
    return db.query(models.User).filter(models.User.email == email).first()


def get_users(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.User).offset(skip).limit(limit).all()


def create_user(db: Session, user: schemas.UserCreate):
    fake_hashed_password = user.password + "notreallyhashed"
    db_user = models.User(email=user.email, hashed_password=fake_hashed_password)
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user


def get_items(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.Item).offset(skip).limit(limit).all()


def create_user_item(db: Session, item: schemas.ItemCreate, user_id: int):
    db_item = models.Item(**item.dict(), owner_id=user_id)
    db.add(db_item)
    db.commit()
    db.refresh(db_item)
    return db_item
```

-   `sql_app/main.py`:

```Python
from fastapi import Depends, FastAPI, HTTPException
from sqlalchemy.orm import Session

from . import crud, models, schemas
from .database import SessionLocal, engine

models.Base.metadata.create_all(bind=engine)

app = FastAPI()


# Dependency
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()


@app.post("/users/", response_model=schemas.User)
def create_user(user: schemas.UserCreate, db: Session = Depends(get_db)):
    db_user = crud.get_user_by_email(db, email=user.email)
    if db_user:
        raise HTTPException(status_code=400, detail="Email already registered")
    return crud.create_user(db=db, user=user)


@app.get("/users/", response_model=list[schemas.User])
def read_users(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    users = crud.get_users(db, skip=skip, limit=limit)
    return users


@app.get("/users/{user_id}", response_model=schemas.User)
def read_user(user_id: int, db: Session = Depends(get_db)):
    db_user = crud.get_user(db, user_id=user_id)
    if db_user is None:
        raise HTTPException(status_code=404, detail="User not found")
    return db_user


@app.post("/users/{user_id}/items/", response_model=schemas.Item)
def create_item_for_user(
    user_id: int, item: schemas.ItemCreate, db: Session = Depends(get_db)
):
    return crud.create_user_item(db=db, item=item, user_id=user_id)


@app.get("/items/", response_model=list[schemas.Item])
def read_items(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    items = crud.get_items(db, skip=skip, limit=limit)
    return items
```

## 46. Необходима директория со статичными файлами. Как бы вы ее привязали к FastAPI?

При помощи специального класса, предоставляемого FastAPI - `StaticFiles`

-   Import `StaticFiles`.
-   "Mount" a `StaticFiles()` instance in a specific path.

```Python
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles

app = FastAPI()

app.mount("/static", StaticFiles(directory="static"), name="static")
```

### What is "Mounting"

"Mounting" means adding a complete "independent" application in a specific path, that then takes care of handling all the sub-paths.

This is different from using an `APIRouter` as a mounted application is completely independent. The OpenAPI and docs from your main application won't include anything from the mounted application, etc.

### Details

The first `"/static"` refers to the sub-path this "sub-application" will be "mounted" on. So, any path that starts with `"/static"` will be handled by it.

The `directory="static"` refers to the name of the directory that contains your static files.

The `name="static"` gives it a name that can be used internally by **FastAPI**.

All these parameters can be different than "`static`", adjust them with the needs and specific details of your own application.

## 47. Как бы вы реализовали простой пайплайн тестирования FastAPI приложения?

### Using TestClient

Import `TestClient`.

Create a `TestClient` by passing your **FastAPI** application to it.

Create functions with a name that starts with `test_` (this is standard `pytest` conventions).

Use the `TestClient` object the same way as you do with `httpx`.

Write simple `assert` statements with the standard Python expressions that you need to check (again, standard `pytest`).

```Python
from fastapi.testclient import TestClient

from .main import app

client = TestClient(app)


def test_read_item():
    response = client.get("/items/foo", headers={"X-Token": "coneofsilence"})
    assert response.status_code == 200
    assert response.json() == {
        "id": "foo",
        "title": "Foo",
        "description": "There goes my hero",
    }


def test_read_item_bad_token():
    response = client.get("/items/foo", headers={"X-Token": "hailhydra"})
    assert response.status_code == 400
    assert response.json() == {"detail": "Invalid X-Token header"}


def test_read_inexistent_item():
    response = client.get("/items/baz", headers={"X-Token": "coneofsilence"})
    assert response.status_code == 404
    assert response.json() == {"detail": "Item not found"}


def test_create_item():
    response = client.post(
        "/items/",
        headers={"X-Token": "coneofsilence"},
        json={"id": "foobar", "title": "Foo Bar", "description": "The Foo Barters"},
    )
    assert response.status_code == 200
    assert response.json() == {
        "id": "foobar",
        "title": "Foo Bar",
        "description": "The Foo Barters",
    }


def test_create_item_bad_token():
    response = client.post(
        "/items/",
        headers={"X-Token": "hailhydra"},
        json={"id": "bazz", "title": "Bazz", "description": "Drop the bazz"},
    )
    assert response.status_code == 400
    assert response.json() == {"detail": "Invalid X-Token header"}


def test_create_existing_item():
    response = client.post(
        "/items/",
        headers={"X-Token": "coneofsilence"},
        json={
            "id": "foo",
            "title": "The Foo ID Stealers",
            "description": "There goes my stealer",
        },
    )
    assert response.status_code == 400
    assert response.json() == {"detail": "Item already exists"}
```

> **Tip**:
> Notice that the testing functions are normal `def`, not `async def`.
> 
> And the calls to the client are also normal calls, not using `await`.
> 
> This allows you to use `pytest` directly without complications.