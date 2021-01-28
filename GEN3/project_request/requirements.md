# Request API
Every API calls need to have the token in the header for auth purposes.

//The list of attribute types should not change that frequently, so that could be another API endpoint to avoid returning name and type for each attrbute. But this would be a minor thing.


## GET /projects
Returns a list of project requests. If the user is a requester it would be a list of the project request submitted by the user. If the user is an approver it would be a list of project requests submitted to the consortium the approver is part of. 
### Response body:
```
[
  {
    'id': int,
    'user_id': int,
    'first_name': string,
    'last_name': string,
    'institution': Institution object / string and company_id,
    'requests': [
        'id': int,
        'consortium': string,
        'state': string,
        'attributes_submitted': [
            'id': int,
            'name': string,
            'type': string,
            'value': string,
            'completed_at': timestamp
        ],
        'attributes_required': [
            'id': int,
            'name': string,
            'type': string
        ]
    ],
    'search_ids': [int]
  }
]
```

## POST /projects
Create a new project request. Based on the data returned by the searches it will create multiple sub-request, one towards each consortium and return the project request object with the list of the required attributes to be collected.
### Request body:
```
{
  'first_name': string,
  'last_name': string,
  'institution': string / id,
  'search_ids': [int],
}
```

### Response body:
```
[
  {
    'id': int,
    'user_id': int,
    'first_name': string,
    'last_name': string,
    'institution': Institution object / string and company_id,
    'requests': [
        'id': int,
        'consortium': string,
        'state': string,
        'attributes_submitted': [
            'id': int,
            'name': string,
            'type': string,
            'value': string,
            'completed_at': timestamp
        ],
        'attributes_required': [
            'id': int,
            'name': string,
            'type': string
        ]
    ],
    'search_ids': [int]
  }
]
```

## POST /attributes
Add an attribute to a request.
### Request body:
```
{
  'project_id': int,
  'attribute_id': id,
  'value': string
}
```

## GET /auth_url
Returns a presigned_url for the user to use to upload a file in S3.
### Response body:
```
{
  'url': string
}
```

## GET /next_state/{request_id}
Checks if all the requested attributes for the state to change have been associated to the project request. If so it will move the request to the next state, otehrwise it will return a list of the missing items.
### Response body:
```
{
  'attribute_missing': [
      'id': int,
      'name': string,
      'type': string
  ],
  code: int
}
```


## GET /messages
Returns a list of messages the user sent or received
### Response body:
```
[
    'sender_id': int,
    'sent_at': timestamp,
    'body': string,
    'sent_to': [int]
]
```

## GET /messages/{request_id}
Returns a list of messages the user sent or received pertinent to a specific request
### Response body:
```
[
    'sender_id': int,
    'sent_at': timestamp,
    'body': string,
    'sent_to': [int]
]
```

## POST /message
Send a message to a list of users.
### Request body:
```
{
    'send_to': [int],
    'body': string
]
```
### Response body:
```
{
    status: int,
    error: string,
}
```
