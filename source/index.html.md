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
wait_for_response_at | Tells the server where to leave its response (i.e. /users/[user_uid]/`[unique_key]`/)
bundle | Extraneous information associated with request `type`

<aside class="notice">
You must include all parameters to ensure a successful response.
</aside>
<aside class="success">
Server will need to respond with all parameters to ensure console compliance.
</aside>


# Console Endpoints

## New User Registration (create_console)

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


>Server Response: /users

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

The server will also need to initialize the `[default_campaign_uid]` with the following Params:

Parameter | Description
--------- | -----------
name | Your First Campaign!
type | email


## Gifty Campaign Creation (create_campaign)

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
  "name" : "[campaign_name]",
  "campaign_types" : [ "email" ]   
  //IMPORTANT! campaign_types is an ARRAY (IE. ["email", "phone"], ["phone"], ["email"] are VALID)
}

```

###Server Response

Write Location | Description
--------- | -----------
[console_id]/active_campaigns|  {..., [campaign_uid]: true } <br> Informs console that `new campaign` is `active` ** MAY BE REDUNDANT
[console_id]/campaigns  | {..., [campaign_uid]: true }  <br>  List of all campaigns - active or inactive ** MAY BE REDUNDANT
campaigns/[campaign_uid] | {[DEFAULT_CAMPAIGN_JSON}  <br>  Initialize the campaign with default campaign Meta


## Add User to Campaign (add_user)

 > This is the JSON sent to `api/v1/requests:

```json
{
  "console_id": "[console_id]",
  "type": "add_user",
  "wait_for_response_at": "/campaigns/[campaign_uid]/[type]",
  "bundle": {
      "name": "[username]",
      "user_type": "[email or phone]",
      "value": "[email_address or phone_number]", //nj.ho@hotmail.com or 403-589-3536
      "campaign_uid": "[campaign_uid]"
    }
}
```

Adds a new user to a campaign.

Parameter | Description
--------- | -----------
console_id | The console_id that contains all information specific to campaign
type | add_user
wait_for_response_at | /campaigns/[campaign_uid]/[type]
bundle | Name: username, user_type: email or phone, value: email address or phone number, campaign_uid: campaign_uid


>Data written to /campaigns/[campaign_uid]/emails

```json
{
  "name": "[username]",
  "value": "[email_address]"
}

```
>Data written to /campaigns/[campaign_uid]/phone

```json
{
  "name": "[username]",
  "value": "[phone_number]"
}

```

###Server Response

Write Location | Description
--------- | -----------
/campaigns/[campaign_uid]/emails |  { "name": [username], "value": [email_address] }<br> Written to this location if `user_type: email`
/campaigns/[campaign_uid]/phone | { "name": [username], "value": [phone_number] }  <br>  Written to this location if `user_type: phone`

<aside class="notice">
Note that both /emails and /phone are both Arrays! Each user is an `Object` in an Array!
ie. [{name, value}, {name, value}]
</aside>


## Delete User from Campaign (remove_campaign_user)

 > This is the JSON sent to `api/v1/requests:

```json
{
  "console_id": "[console_id]",
  "type": "remove_campaign_user",
  "wait_for_response_at": "/campaigns/[campaign_uid]/[type]",
  "bundle": {
    "user_type": "[email/phone]",
    "index": "[key/index in /campaigns/[campaign_uid]/[type] to be removed]",
    "campaign_uid": "campaign_uid"
    }
}
```

Assigns a new user to a campaign.

Parameter | Description
--------- | -----------
console_id | The console_id that contains all information specific to campaign
type | remove_campaign_user
wait_for_response_at | /campaigns/[campaign_uid]/[type] 
bundle | Name: username, user_type: email or phone, campaign_uid: campaign_uid


###Server Response

Write Location | Description
--------- | -----------
/campaigns/[campaign_uid]/emails |  Remove `[index]` from `Array` at endpoint
/campaigns/[campaign_uid]/phone | Remove `[index]` from `Array` at endpoint

<aside class="notice">
Note that both /emails and /phone are both Arrays! Each user is an Object in an Array!
ie. [{name, value}, {name, value}]
</aside>


## Default Campaign Gifty(assign_campaign_default)

 > This is the JSON sent to `api/v1/requests:

```json
{
  "console_id": "[console_id]",
  "type": "assign_campaign_default",
  "wait_for_response_at": "/campaigns/[campaign_uid]/[default_email_gift or default_phone_value]",
  "bundle": {
    "campaign_uid": "[campaign_uid]",
    "type": "[default_email_gift/default_phone_value]",
    "value": "[gifty_uid (type default_email_gift) or cents (default_phone_value)]"
    }
}
```

Assigns a `default_email_gift` or a `default_phone_value`

Parameter | Description
--------- | -----------
console_id | The console_id that contains all information specific to campaign
type | assign_campaign_default
wait_for_response_at | /campaigns/[campaign_uid]/[default_email_gift or default_phone_value]
bundle | campaign_uid: campaign_uid, type: [default_email_gift/default_phone_value], "value": [gifty_uid (type default_email_gift] [cents (default_phone_value)]


###Server Response

Write Location | Description
--------- | -----------
/campaigns/[campaign_uid]/[default_email_gift] |  Update with `[gifty_uid]`
/campaigns/[campaign_uid]/[default_phone_value] | Update with `[cents]`

## Update Gifty (update_gifty)

 > This is the JSON sent to `api/v1/requests:

```json
{
  "console_id": "[console_id]",
  "type": "update_gifty",
  "wait_for_response_at": "/consoles/[console_id]/giftys/[gifty_uid]",
  "bundle": {
    "gifty_id": "[gifty_id/null]", // null if is a new Gifty
    "type": "[new or update]"
    "value": "[gifty_object]" // A complete Gifty Object is yet to be defined at this point
    }
}
```

Updates an existing Gifty, or pushes a new Gifty Object.

Type `new` will require server to `push` a new _Gifty Object_ to the server.
Type `update` will require server to update existing _Gifty Object_. 

**The Gifty Object must be updated at both /offers and in each /consoles/[console_id]/giftys/[gifty_uid]!**


Parameter | Description
--------- | -----------
console_id | The console_id that contains all information specific to campaign
type | update_gifty
wait_for_response_at | /consoles/[console_id]/giftys/[gifty_id]
bundle | gifty: gifty_id, type: [new/update] value: [gifty_object]

**Note: Gifty Object structure is yet to be determined. It will be very similar to what is existing**


###Server Response
if `bundle.type: new`, push Gifty Object.
if `bundle.type: update` update Gifty Object.

Write Location | Description
--------- | -----------
/consoles/[console_id]/giftys/[gifty_id]|  Update/push with `[gifty_object]`
/offers/[gifty_id] | Update/push with `[gifty_object]`
