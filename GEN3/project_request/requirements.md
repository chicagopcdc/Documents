# Request API

Every API calls need to have the token in the header for auth purposes.

//The list of attribute types should not change that frequently, so that could be another API endpoint to avoid returning name and type for each attrbute. But this would be a minor thing.

## GET /projects

Returns a list of project requests. If the user is a requester it would be a list of the project request submitted by the user. If the user is an approver it would be a list of project requests submitted to the consortium the approver is part of.

### Response body:

```jsonc
[
  {
    "id": 0, // number (int)
    "name": "", // string
    "description": "", // string
    "researcher": {
      "user_id": 0, // number (int)
      "first_name": "", // string
      "last_name": "", // string
      "institution": "" // string
    },
    "requests": [
      {
        "id": 0, // number (int)
        "consortium": "", // string
        "state": "", // string; we may want to have state_id instead and add a state endpoint
        "attributes": [
          {
            "id": 0, // number (int)
            "name": "", // string
            "type": "", // string
            "value": "", // string
            "completed_at": "", // string (timestamp)
            "optional": true // bool
          }
        ]
      }
    ],
    "search_ids": [] // array of number (int)
  }
]
```

## POST /projects

Create a new project request. Based on the data returned by the searches it will create multiple sub-request, one towards each consortium and return the project request object with the list of the required attributes to be collected.

### Request body:

```jsonc
{
  "name": "", // string
  "description": "", // string
  "search_ids": [] // array of number (int)
}
```

### Response body:

```jsonc
[
  {
    "id": 0, // number (int)
    "name": "", // string
    "description": "", // string
    "researcher": {
      "user_id": 0, // number (int)
      "first_name": "", // string
      "last_name": "", // string
      "institution": "" // string
    },
    "requests": [
      {
        "id": 0, // number (int)
        "consortium": "", // string
        "state": "", // string
        "attributes": [
          {
            "id": 0, // number (int)
            "name": "", // string
            "type": "", // string
            "value": "", // string
            "completed_at": "", // string (timestamp)
            "optional": true // bool
          }
        ]
      }
    ],
    "search_ids": [] // array of number (int)
  }
]
```

## POST /attributes

Add an attribute to a request.

### Request body:

```jsonc
{
  "project_id": 0, // number (int)
  "attribute_id": 0, // number (int)
  "value": "" // string
}
```

## GET /auth_url

Returns a presigned_url for the user to use to upload a file in S3.

### Response body:

```jsonc
{
  "url": "" // string
}
```

## GET /submit/{request_id}

Checks if all the requested attributes for the state to change have been associated to the project request. If so it will move the request to the next state, otehrwise it will return a list of the missing items.

### Response body:

```jsonc
{
  "attribute_missing": [
    {
      "id": 0, // number (int)
      "name": "", // string
      "type": "" // string
    }
  ],
  "state": "" // string
}
```

## GET /messages

Returns a list of messages the user sent or received

### Response body:

```jsonc
[
  {
    "sender_id": 0, // number (int)
    "sent_at": "", // string (timestamp)
    "body": "", // string
    "sent_to": [] // array of number (int)
  }
]
```

## GET /messages/{request_id}

Returns a list of messages the user sent or received pertinent to a specific request

### Response body:

```jsonc
[
  {
    "sent_at": "", // string (timestamp)
    "body": "", // string
    "sender_name": "", // string
    "self": true // bool
  }
]
```

## POST /message

Send a message to a list of users.

### Request body:

```jsonc
{
  "request_id": 0, // number (int)
  "body": "" // string
}
```

### Response body:

```jsonc
{
  "status": 200, // number (int; HTTP status code)
  "error": "" //string
}
```
