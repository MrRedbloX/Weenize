# Weenize API
## Endpoint
https://weenize.nw.r.appspot.com/
## Headers
client-id: \<YOUR CLIENT ID>  
client-secret: \<YOU CLIENT SECRET>
## Endpoints
### GET api/healthcheck
Returns 200 if the API is available.
### POST api/run
Runs directly the weez payload.
- Body: the same as weez body.
- Response 200: the same as weez response.
### PUT api/task
Creates a task.
- Body: the same as weez body.
- Response 201: Task.
### GET api/task
Gets a task.
- Path params:
    - id: the task id.
- Response 200: Task.
### GET api/task/all
Gets all tasks.
- Response 200: list\<Task>
### POST api/task/state
Allows to change the state of a task.
- Path params:
    - id: the task id.
    - active: "true" to set the task active.
- Response 200: timestamp.
### POST api/task/run
Runs a task.
- Path params:
    - id: the task id.
- Response 200: the task id.
### GET api/data/all
Gets all the data from a task.
- Path params: 
    - task_id: the id of the task.
- Response 200: TsData
### GET api/data/last
Gets the last data of a task.
- Path params: 
    - task_id: the id of the task.
- Response 200: the weez data.
### GET api/data/timestamp
Gets the data corresponding to a given timestamp.
- Path params: 
    - task_id: the id of the task.
    - ts: the timestamp.
- Response 200: the weez data.
### GET api/data/timestamp/bounds
Get data within timestamp bounds.
- Path params: 
    - task_id: the id of the task.
    - start_ts: the start timestamp.
    - end_ts: the end timestamp.
- Response 200: the weez data.
### GET api/runs/all
Gets all the runs of very tasks of a client.
- Response 200: list\<Runs>.
### GET api/runs/state
Gets all the runs of very tasks of a client.
- Path params: 
    - success: "true" to only get runs which have succeedeed.
- Response 200: list\<Runs>.
### PUT api/admin/client
(Admin only) Creates a client.
- Body: encrypted following data:
```
{
    "name": "",
    "email": "",
    "phone": "",
    "card": {
        "number": "",
        "exp_month": 0,
        "exp_year": 0,
        "cvc": ""
    }
}
```
- Path params: 
    - use_default_service_account: "true" to attach default service account credentials.
- Response 201: Client.
## Models
### Task
```
{
    "id": "<task_id>",
    "client_id": "<client-id>",
    "active": true,
    "task": {
        <weez template>
    }
}
```
### TsData
```
{
    "<timestamp>": {
        <weez data>
    }
}
```
### Runs
```
{
    "client_id": "<client-id>",
    "expires_at": "<datetime>"
    "message": "<message>"
    "success": <bool>
    "task_id": "<task_id>"
    "timestamp": <float>
}
```
### Client
```
{
    "active": true,
    "client_id": "<client-id>",
    "client_secret": "<client_secret>",
    "consumption": 0,
    "customer_id": "<stripe_customer_id>"
}
```