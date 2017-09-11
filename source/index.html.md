---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ

  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Gifty API! You can use our API to access Gifty API endpoints.

This example API documentation page was created with [Slate](https://github.com/tripit/slate). Feel free to edit it and use it as a base for your own API's documentation.

# General API Request Structure

> Gifty has a single endpoint for all requests. Contains following information. 


```json
{
  "console_id": "[console id information]",
  "type": "[request type]",
  "wait_for_response_at": "[e.g. -J186Gs889iH_s]",
  "bundle": {
      "[extra_information]": "[email/phone]"
    }
}
```

Gifty has a single endpoint which contains all information necessary for the server to respond appropriately.

Requests are written to `https://ip-gifty-staging.firebaseio.com/` + `api/v1/requests/[unique_key]` containing the appropriate information.

Parameter | Description
--------- | -----------
console_id | The console_id that contains all information specific to campaign
type | Type of request (ie. `create_campaign`)
wait_for_response_at | Informs API where to write information to in database
bundle | Extraneous information associated with request `type`

<aside class="notice">
You must include all parameters to ensure a successful response.
</aside>
<aside class="success">
Server will need to respond with all parameters to ensure console compliance.
</aside>


# Console Endpoints

## TODO: New User Registration

 > This is the JSON sent to `api/v1/requests:

```json
{
  "console_id": null,
  "type": "create_console",
  "wait_for_response_at": "/users/[user_uid]"
}
```

Assign a newly registered **user** a **console_id**

A new user registration should AUTOMATICALLY trigger the required response.
The `create_console` will be available as an additional convenience API call.


Parameter | Description
--------- | -----------
console_id | The console_id that contains all information specific to campaign
type | create_console
wait_for_response_at | /users/[user_uid]


>/users

```json
{
  ...,
  "[user_uid]": "[console_id]"
}

```
###Server Response

>DEFAULT_CONSOLE_JSON

```json
{
  "active_campaigns" : {
    "[default_campaign_uid]" : true
  },
  "agreements" : {
    "[default_agreement_uid]" : true
  },
  "campaigns" : {                         /*This holds ALL campaigns related to this console_id*/
    "[default_campaign_uid]" : true 
  },
  "console_creation_date" : [1498869747], /*Unix Time of console creation*/
  "name" : "Console One",                 /*Name to be displayed in Console Sidebar. Input yet to be determined*/
  "notifications" : {
    "key" : "timestamp"
  },
  "payment" : {
    "balance" : {
      "cad" : {
        "cents" : 0
      },
      "token": null
    }
  },
  "console_id" : ["unique_console_id"]   /*Matches the console_id which this data is nested in. Facilitates dashboard queries*/
}

```

Write Location | Description
--------- | -----------
/users/ |  {..., [user_uid]: "[console_id]" } <br> Assigns the console to the user.
/consoles/[console_id] | {DEFAULT_CONSOLE_JSON} <br> Contains minimum requirements for console. Server will assign a [default_agreement_uid] and [default_campaign_uid]


## TODO: Gifty Campaign Creation 



 > This is the JSON sent to `api/v1/requests:

```json
{
  "console_id": "[console_id]",
  "type": "create_campaign",
  "wait_for_response_at": "/consoles/[console_id]/campaigns/[campaign_uid]",
  "bundle": {
      "name": "[campaign_name]",
      "type": "[email or phone]"
    }
}
```

Create a new campaign of type **email** or **phone** that is assigned to a **console**:

Parameter | Description
--------- | -----------
console_id | The console_id that contains all information specific to campaign
type | create_campaign
wait_for_response_at | /consoles/[console_id]/campaigns/[campaign_uid]
bundle | Name: Campaign Name, type: email/phone

**Note: Future changes will allow ability for type email AND phone.

>Data written to [console_id]/active_campaigns

```json
{
  ...,
  "[campaign_uid]": true 
}

```
>Data written to [console_id]/campaigns 

```json
{
  ...,
  "[campaign_uid]": true 
}

```

>DEFAULT_CAMPAIGN_JSON

```json
{
  "campaign_types" : [ "email" ]   
  //IMPORTANT! This is an ARRAY (IE. ["email", "phone"], ["phone"], ["email"] are VALID)
}

```

###Server Response

Write Location | Description
--------- | -----------
[console_id]/active_campaigns|  {..., [campaign_uid]: true } <br> Informs console that `new campaign` is `active` ** MAY BE REDUNDANT
[console_id]/campaigns  | {..., [campaign_uid]: true }  <br>  List of all campaigns - active or inactive ** MAY BE REDUNDANT
campaigns/[campaign_uid] | {[DEFAULT_CAMPAIGN_JSON}  <br>  Initialize the campaign with default campaign Meta



# DO NOT USE/Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint retrieves a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

