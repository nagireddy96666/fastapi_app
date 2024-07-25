# fastapi_app
FastAPI automatically generates interactive API documentation using Swagger UI. You just need to run the application, and you can access the Swagger UI to interact with the CRUD API.

Step 1: Ensure Your main.py is Ready
Here's the updated main.py which you should already have:
# fastapi_crud_app/app/main.py
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Dict

app = FastAPI()

class Item(BaseModel):
    name: str
    description: str = None
    price: float
    tax: float = None

items: Dict[int, Item] = {}

@app.post('/items/', response_model=Item)
def create_item(item: Item):
    item_id = len(items) + 1
    items[item_id] = item
    return item

@app.get('/items/{item_id}', response_model=Item)
def read_item(item_id: int):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return items[item_id]

@app.put('/items/{item_id}', response_model=Item)
def update_item(item_id: int, item: Item):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    items[item_id] = item
    return item

@app.delete('/items/{item_id}')
def delete_item(item_id: int):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    del items[item_id]
    return {"message": "Item deleted successfully"}

@app.get('/items/', response_model=Dict[int, Item])
def list_items():
    return items
Step 2: Ensure Your requirements.txt is Ready
Make sure your requirements.txt contains the following:
fastapi
uvicorn
Step 3: Ensure Your Dockerfile is Ready
Ensure your Dockerfile looks like this:
# fastapi_crud_app/Dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

COPY ./app /app

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]

Step 4: Build and Run the Docker Container
Navigate to the project directory:
cd path/to/fastapi_crud_app
2.Build the Docker image:
sudo docker build -t fastapi_crud_app .

3.Run the Docker container:
sudo docker run -d --name fastapi_crud_app -p 8000:8000 fastapi_crud_app
try in browser 
http://192.168.154.128:8000/items/

{
  "1": {
    "name": "Updated Item1",
    "description": "This is updated item 1",
    "price": 12.5,
    "tax": 3
  },
  "3": {
    "name": "Item1",
    "description": "This is item 3",
    "price": 11.5,
    "tax": 2.5
  },
  "4": {
    "name": "Item1",
    "description": "This is item 1",
    "price": 10.5,
    "tax": 2.5
  },
  "5": {
    "name": "Item1",
    "description": "This is item 1",
    "price": 10.5,
    "tax": 2.5
  }
}
