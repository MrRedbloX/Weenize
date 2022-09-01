# Weenize API
## Endpoint
https://weenize.nw.r.appspot.com/
## Headers
### Bearer
Required for almost all endpoints
Authorization: Bearer \<JWT TOKEN>
### Basic
Required only for login endpoint.
Authorization: Basic \<BASE64 TOKEN>
## Common
### GET api/healthcheck
Returns 200 if the API is available.
### GET api/token
Returns a token from client id and secret.
- Headers: client-id and client-secret.
- Response 200: Token.
### GET api/token/refresh
Returns a token from a refresh token.
- Body:
```
{
    refresh_token: ""
}
```
- Response 200: Token.
### POST api/run
Runs directly the weez payload.
- Body: the same as weez body.
- Response 200: the same as weez response.
## Task
### PUT api/task
Creates a task.
- Body: the same as weez body.
- Path params:
    - cron: cron string for execution cycle.
- Response 201: Task.
### GET api/task
Gets a task.
- Path params:
    - id: the task id.
- Response 200: Task.
### GET api/task/all
Gets all tasks.
- Response 200: list\<Task>
### PATCH api/task
Allows to update a task.
- Path params:
    - id: the task id.
    - active: "true" to set the task active.
- Response 200: task id.
### POST api/task/run
Runs a task.
- Path params:
    - id: the task id.
- Response 200: the task id.
## Data
### GET api/data/all
Gets all the data from a task.
- Path params: 
    - task_id: the id of the task.
    - type: the type of the data (can be data or aggregation).
- Response 200: list\<Data>
### GET api/data/last
Gets the last data of a task.
- Path params: 
    - task_id: the id of the task.
    - type: the type of the data (can be data or aggregation).
- Response 200: Data.
### GET api/data/timestamp
Gets the data corresponding to a given timestamp.
- Path params: 
    - task_id: the id of the task.
    - ts: the timestamp.
    - type: the type of the data (can be data or aggregation).
- Response 200: Data.
### GET api/data/timestamp/bounds
Gets data within timestamp bounds.
- Path params: 
    - task_id: the id of the task.
    - start_ts: the start timestamp (optional).
    - end_ts: the end timestamp (optional).
    - type: the type of the data (can be data or aggregation).
- Response 200: list\<Data>.
## Client
### PUT api/client
Creates a client.
- Body:
```
{
    "name": "",
    "email": "",
    "password": "",
    "phone": "",
    "card": {
        "number": "",
        "exp_month": 0,
        "exp_year": 0,
        "cvc": ""
    }
}
```
- Response 201: Client.
### GET api/client/login
Gets the client id and secret from basic authentification.
- Response 200: Token
### GET api/client
Gets client information.
- Response 200: Client.
### PUT api/client/plans
Attaches plans to client.  
The plans are the following: google_cloud_platform, twitter.
- Path params: 
    - plans: comma separated string of plans.
- Response 200: client id.
### DELETE api/client/plans
Deletes plans attached to client.
- Path params: 
    - plans: comma separated string of plans.
- Response 200: client id.
## Models
### Task
```
{
    "id": "<task_id>",
    "client_id": "<client-id>",
    "active": true,
    "task": {
        <weez template>
    },
    "cron": "",
    "last_run": 0.0,
    "running": false,
    "job_name": ""
}
```
### Data
```
{
    "metadata": {
        "timestamp": 0.0,
        "client_id": "",
        "success": true,
        "message": "",
        "task_id": ""
    },
    "data": {
        <weez data>
    }
}
```
### Client
```
{
    "active": true,
    "client_id": "<client-id>",
    "client_secret": "<client_secret>",
    "consumption": 0,
    "customer_id": "<stripe_customer_id>",
    "plans": [""],
    "args": {
        <any args>
    }
}
```
### Token
````
{
    "access_token": "",
    "refresh_token": "",
    "expires": "" // Date UTC ISO format
}
```