# Template-model-api
This repo contains a complete example of a working api developed with FastAPI.  
As opposed of the TemplateAPI, which is a general example of API, this example has the specific purpose of a template for the model serving management.  
The entities used in this example are stored in a local sqllite DB and they are:
- users: those who can login and use the other APIs
```
    user: {
        id: int
        name: str
        email: str
        password: str
    }
```
- featuresVector: the features used as input for predicting an output prediction.
```
    featuresVector: {
        id: int
        value: float
        feature_idx: int
        prediction_id: int
    }
```
- prediction: outcome of the prediction model.
```
    prediction: {
        id: int
        details: str
        prediction_date: datetime
        prediction: float
        user_id: int
    }
```

## Installation
- clone this repository
- create and activate a virtual environment
- install the requirements by running:
```
    pip install -r requirements.txt
```
- create a file .env containing:
```
    SECRET_KEY = "" #use your own alphanumeric string
    ALGORITHM = "HS256"
    SQLALCHEMY_DATABASE_URL = "sqlite:///./mdi.db"  
    CREATE_USER_KEY = "" #use your own alphanumeric string
```

## Usage
The application server can be started by running: 
``` 
    uvicorn model_template.main:app --reload
```
Once the application startup has been completed, the requests can be forwarded to the API.  
A simple example of an API call with authentication has been implemented in the file `api_caller.py`.  
Moreover, a file named `user_client.py` has been created for the user management (create, show, delete).
### Create user
A new user can be created running:  
`python3 user_client.py create --name NAME --email EMAIL --password PASSWORD`
### Show all users 
A list of the previously created users can be retrieved running:  
`python3 user_client.py show`
### Delete a user
An existing user can be created running:  
`python3 user_client.py delete --id ID`

## Repository structure
```
├── model_template/
│   ├── repository/
│   │   ├── modelML.py  # contains the actions triggered by the api calls of the model 
│   │   └── user.py # contains the actions triggered by the api calls of the user resources 
│   │
│   ├── routers/
│   │   ├── __init__.py
│   │   ├── authentication.py   # contains the API methods related to the login process
│   │   ├── modelml.py              # contains the API methods related to the model resource
│   │   └── user.py             # contains the API methods related to the user resources
│   │
│   ├── __init__.py
│   ├── database.py     # contains the utils and startup functions related to the db
│   ├── hashing.py      # contains the utils functions related to the password encryption
│   ├── main.py         # main file of this project
│   ├── models.py       # contains the entities of the tables used in the db
│   ├── oauth2.py       # contains the utils functions used for the auth with bearer token
│   ├── schemas.py      # contains the pydantic classes used i the APIs
│   └── token.py        # contains the utils functions related to the management of the token
│   
├── .gitignore
├── README.md           # this file
├── api_caller.py       # example of python client code
├── user_client.py      # client for user management         
├── mdi.db              # sqllite db instance 
├── requirements.txt    
└── sandbox.py          # file used to test some code
```

## Available APIs
All the available APIs and the FastAPI autogenerated documentation can be found at: http://server-url:8000/docs

### Authentication
- Login: http://server-url:8000/login  
    POST method  
    Used to get the bearer token  
    ```
    Input: {                # as request data 
        "username": str, 
        "password": str
    } 
    ```
    ```
    Output: {
        "access_token": access_token, 
        "token_type": "bearer"
    }
    ```

### Model  
All these APIs are locked and a valid access token must be provided (as in the api_caller.py example) to run successfully  
- Predict: http://server-url:8000/modelml  
    POST method
    Used to get the model predictions
    ```
    Input: {                # as request data 
        "feat1": [int], 
        "feat2": [int], 
        "feat3": [int] 
    } 
    ```
    ```
    Output: [
        {
            "prediction": float
        }
    ]
    ```
- Predict: http://server-url:8000/modelml  
    GET method
    Used to get the model info
    ```
    Input: {} 
    ```
    ```
    Output: [
        {
            "data": "This method returns model's info 
        }
    ]
    ```


### User
All these APIs are locked and a CREATE_USER_KEY must be provided (as in the user_client.py example) to run successfully
- Get all user: http://server-url:8000/user  
    GET method  
    Used to retrieve the mdi with a specific id
    ```
    Input: {}     
    ```
    ```
    Output: {       # entity of Class ShowUser from schema.py
        "id": int
        "name": str, 
        "email": str 
    }
    ```
- Get user: http://server-url:8000/user/{id}  
    GET method  
    Used to retrieve the mdi with a specific id
    ```
    Input: {id}     # id as query param 
    ```
    ```
    Output: {       # entity of Class ShowUser from schema.py
        "id": int
        "name": str, 
        "email": str
    }
    ```
- Create: http://server-url:8000/user \
    POST method \
    Used to create a new user 
    ```  
    Input: {            # as request data
        "id": 0,        # the autoincrement is enabled
        "name": str, 
        "email": str,
        "password": str
    }     
    ```
    ```
    Output: {
        "id": int,        
        "name": str, 
        "email": str,
    }
    ```
- Delete: http://server-url:8000/user/{id}  
    GET method  
    Used to delete a user with specific id
    ```
    Input: {id}     # id as query param 
    ```
    ```
    Output: {       # entity of Class ShowUser from schema.py
        "id": int
        "name": str, 
        "email": str
    }
    ```

## Improvements
- Create a Docker container to run this project.
- Something for redeployment or automatic retraining of the model.

## Useful apps
- TablePlus: explore the local db - https://tableplus.com

## References
FastAPI - https://fastapi.tiangolo.com \
SQLAlchemy - https://www.sqlalchemy.org 
