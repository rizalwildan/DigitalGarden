# Fast API

[FastAPI](https://fastapi.tiangolo.com/) is a modern, fast (high-performance), web framework for building APIs with Python 3.6+ based on standard Python type hints.

The key feature are:

* Fast: Very high performance, on par with NodeJS and Go (thanks to Starlette and Pydantic). One of the fastest Python frameworks available.
* Fast to code: Increase the speed to develop features by about 200% to 300%.
* Fewer bugs: Reduce about 40% of human (developer) induced errors.
* Intuitive: Great editor support. Completion everywhere. Less time debugging.
* Easy: Designed to be easy to use and learn. Less time reading docs.
* Short: Minimize code duplication. Multiple features from each parameter declaration. Fewer bugs.
* Robust: Get production-ready code. With automatic interactive documentation.
* Standards-based: Based on (and fully compatible with) the open standards for APIs: OpenAPI (previously known as Swagger) and JSON Schema.
* Authentication with JWT: with a super nice tutorial on how to set it up.

### Installation

```bash
pip install fastapi
```

You will also need an ASGI server, for production such as Uvicorn or Hypercorn.

```bash
pip install "uvicorn[standard]"
```

### Simple Example

Create a file `main.py` with:

```python
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: Optional[str] = None):
    return {"item_id": item_id, "q": q}

```

Run the server from command line:

```bash
uvicorn main:app --reload
```

Or you can run from python:

```python
import uvicorn
if __name__ == "__main__":
  uvicorn.run("main:app", host="0.0.0.0", port=8000, reload=True)
```

Open your browser at [http://127.0.0.1:8000/items/5?q=somequery](http://127.0.0.1:8000/items/5?q=somequery). You will see the JSON response as:

```json
{
  "item_id": 5,
  "q": "somequery"
}
```

You already created an API that:

* Receives HTTP requests in the paths `/` and `/items/{item_id}`.
* Both paths take GET operations (also known as HTTP methods).
* The path `/items/{item_id}` has a path parameter `item_id` that should be an `int`.
* The path `/items/{item_id}` has an optional `str` query parameter `q`.
* Has interactive API docs made for you:
* Swagger: [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs).
* Redoc: [http://127.0.0.1:8000/redoc](http://127.0.0.1:8000/redoc).

You will see the automatic interactive API documentation (provided by Swagger UI)

### Sending data to the server

When you need to send data from a client (let's say, a browser) to your API, you have three basic options:

* As [path parameters](https://lyz-code.github.io/blue-book/fastapi/#path-parameters) in the URL (`/items/2`).
* As [query parameters](https://lyz-code.github.io/blue-book/fastapi/#query-parameters) in the URL (`/items/2?skip=true`).
* In the [body](https://lyz-code.github.io/blue-book/fastapi/#body-requests) of a POST request.

To send simple data use the first two, to send complex data or sensitive data use the last. It also support sending data through [cookies](https://fastapi.tiangolo.com/tutorial/cookie-params/) and [headers](https://fastapi.tiangolo.com/tutorial/header-params/).

### Path parameter

You can declare path "parameters" or "variables" with the same syntax used by Python format strings:

```python
@app.get("/items/{item_id}")
def read_item(item_id: int):
    return {"item_id": item_id}
```

If you define the type hints of the function arguments, FastAPI will use [pydantic](https://lyz-code.github.io/blue-book/coding/python/pydantic/) data validation.

If you need to use a Linux path as an argument, check [this workaround](https://fastapi.tiangolo.com/tutorial/path-params/#path-parameters-containing-paths), but be aware that it's not supported by OpenAPI.

### Order matters

Because path operations are evaluated in order, you need to make sure that the path for the fixed endpoint `/users/me` is declared before the variable one `/users/{user_id}`:

```python
@app.get("/users/me")
async def read_user_me():
    return {"user_id": "the current user"}


@app.get("/users/{user_id}")
async def read_user(user_id: str):
    return {"user_id": user_id}
```

Otherwise, the path for `/users/{user_id}` would match also for `/users/me`, "thinking" that it's receiving a parameter user\_id with a value of "me".

### Predefined value

If you want the possible valid path parameter values to be predefined, you can use a standard Python `Enum`.

```python
from enum import Enum


class ModelName(str, Enum):
    alexnet = "alexnet"
    resnet = "resnet"
    lenet = "lenet"


@app.get("/models/{model_name}")
def get_model(model_name: ModelName):
    if model_name == ModelName.alexnet:
        return {"model_name": model_name, "message": "Deep Learning FTW!"}

    if model_name.value == "lenet":
        return {"model_name": model_name, "message": "LeCNN all the images"}

    return {"model_name": model_name, "message": "Have some residuals"}
```

These are the basics, FastAPI supports more complex [path parameters and string validations](https://fastapi.tiangolo.com/tutorial/path-params-numeric-validations/).

### Query parameter

When you declare other function parameters that are not part of the path parameters, they are automatically interpreted as "query" parameters.

```python
fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip : skip + limit]
```

The query is the set of key-value pairs that go after the `?` in a URL, separated by `&` characters.

For example, in the URL: [http://127.0.0.1:8000/items/?skip=0\&limit=10](http://127.0.0.1:8000/items/?skip=0\&limit=10)

These are the basics, FastAPI supports more complex [query parameters and string validations](https://fastapi.tiangolo.com/tutorial/query-params-str-validations/).

### Request Body

To declare a request body, you use Pydantic models with all their power and benefits.

```python
from typing import Optional
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


@app.post("/items/")
async def create_item(item: Item):
    return item
```

With just that Python type declaration, FastAPI will:

* Read the body of the request as JSON.
* Convert the corresponding types (if needed).
* Validate the data: If the data is invalid, it will return a nice and clear error, indicating exactly where and what was the incorrect data.
* Give you the received data in the parameter `item`.
* Generate JSON Schema definitions for your model.
* Those schemas will be part of the generated OpenAPI schema, and used by the automatic documentation UIs.

These are the basics, FastAPI supports more complex patterns such as:

* [Using multiple models in the same query](https://fastapi.tiangolo.com/tutorial/body-multiple-params/).
* [Additional validations of the pydantic models](https://fastapi.tiangolo.com/tutorial/body-fields/).
* [Nested models](https://fastapi.tiangolo.com/tutorial/body-nested-models/).
