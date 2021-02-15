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
      "id": 0, // number (int)
      "first_name": "", // string
      "last_name": "", // string
      "institution": "" // string
    },
    "requests": [
      {
        "id": 0, // number (int)
        "consortium": "", // string
        "state": "", // string; we may want to have state_id instead and add a state endpoint
        "submitted_at": "", // string (timestamp) or null
        "completed_at": "", // string (timestamp) or null
        "attributes": [
          {
            "id": 0, // number (int)
            "name": "", // string
            "type": "", // string
            "value": "", // string
            "optional": true // bool
          }
        ],
        "available_user_actions": [] // array of string ("SUBMIT" | "REQUEST_UPDATE" | "APPROVE" | "REJECT")
      }
    ],
    "searches": [
      {
        "name": "", // string
        "description": "", // string
        "filters": "" // string (serialized JSON)
      }
    ]
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

Same as the `GET /projects` response.

## PATCH /projects/{project_id}

Updates project data. Only name and description values are available to updates (see Request body below).

### Request body:

```jsonc
{
  "name": "", // string
  "description": "" // string
}
```

### Response body:

```jsonc
{
  "status": 200, // number (int; HTTP status code)
  "error": "" //string
}
```

## GET /requests

Returns a list of data requests with added information on requester user ("principal investigator"). Handles query string `?consortium={consortium_name}` to filter the list by consortium.

```jsonc
[
  {
    "id": 0, // number (int)
    "consortium": "", // string
    "state": "", // string
    "submitted_at": "", // string (timestamp) or null
    "completed_at": "", // string (timestamp) or null
    "attributes": [
      {
        "id": 0, // number (int)
        "name": "", // string
        "type": "", // string
        "value": "", // string
        "optional": true // bool
      }
    ],
    "available_user_actions": [], // array of string ("SUBMIT" | "REQUEST_UPDATE" | "APPROVE" | "REJECT")
    "project": {
      "id": 0, // number (int)
      "title": "", // string
      "description": "" // string
    },
    "researcher": {
      "id": 0, // number (int)
      "first_name": "", // string
      "last_name": "", // string
      "institution": "" // string
    }
  }
]
```

## POST /user-action

Dispatches a user action, which triggers updates to the relevant data request's `state` as well as `attributes`.

User actions are one of the following: `SUBMIT`, `REQUEST_UPDATE`, `APPROVE`, and `REJECT`. Action type `SUBMIT` is only avilable to requester ("researcher") user, and the other three action types are only available to reviewer user. Updating `attributes` is available for `SUBMIT` action only.

### Request body

```jsonc
{
  "type": "", // string ("SUBMIT" | "REQUEST_UPDATE" | "APPROVE" | "REJECT")
  "payload": {
    // request_id is required for all action types
    "request_id": 0, // number (int)

    // attributes is available for "SUBMIT" action type only
    "attributes": [
      {
        "id": 0, // number (int)
        "value": "" // string
      }
    ]
  }
}
```

### Response body:

```jsonc
{
  "status": 200, // number (int; HTTP status code)
  "error": "" //string
}
```

## POST /file_upload_url

Returns a pre-signed URL for uploading a file to S3. The frontend application can then make a `PUT` request to upload binary data directly to the signed URL.

### Request body:

```jsonc
{
  "filename": "" // string; {user_id}_{project_id}_{attribute_id}_{timestamp}.{ext}
}
```

### Response body:

```jsonc
{
  "url": "" // string
}
```

## GET /messages

Returns a list of messages for each request. Handles query string `?request_id={request_id}` to filter the list by request.

### Response body:

```jsonc
[
  {
    "request_id": 0, // number (int)
    "sender": {
      "id": 0, // number (int)
      "fist_name": "", // string
      "last_name": "" // string
    },
    "sent_at": "", // string (timestamp)
    "body": "" // string
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
