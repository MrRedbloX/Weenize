# Weenize API
## Endpoint
https://weenize.nw.r.appspot.com/
## Headers
### Client
client-id: \<YOUR CLIENT ID>  
client-secret: \<YOU CLIENT SECRET>
### Admin
x-api-key: \<YOUR API KEY>
## Common
### GET api/healthcheck
Returns 200 if the API is available.
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
- Response 200: list\<Data>
### GET api/data/last
Gets the last data of a task.
- Path params: 
    - task_id: the id of the task.
- Response 200: Data.
### GET api/data/timestamp
Gets the data corresponding to a given timestamp.
- Path params: 
    - task_id: the id of the task.
    - ts: the timestamp.
- Response 200: Data.
### GET api/data/timestamp/bounds
Gets data within timestamp bounds.
- Path params: 
    - task_id: the id of the task.
    - start_ts: the start timestamp.
    - end_ts: the end timestamp.
- Response 200: list\<Data>.
## Client
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
## Admin
### PUT api/admin/client
Creates a client.
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