[![Dependency Status](https://david-dm.org/alphagov/notifications-node-client.svg)](https://david-dm.org/alphagov/notifications-node-client)

# GOV.UK Notify Node.js client

## Installation
```shell
npm install notifications-node-client
```

## Getting started
```javascript
var NotifyClient = require('notifications-node-client').NotifyClient,

notifyClient = new NotifyClient(apiKey);
```

Generate an API key by logging in to
[GOV.UK Notify](https://www.notifications.service.gov.uk) and going to
the _API integration_ page.

## Send messages

### Text message

```javascript
notifyClient.sendSms(templateId, phoneNumber, personalisation, reference);
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `Object`:

```javascript
{
    "id": "bfb50d92-100d-4b8b-b559-14fa3b091cda",
    "reference": None,
    "content": {
        "body": "Some words",
        "from_number": "40604"
    },
    "uri": "https://api.notifications.service.gov.uk/v2/notifications/ceb50d92-100d-4b8b-b559-14fa3b091cd",
    "template": {
        "id": "ceb50d92-100d-4b8b-b559-14fa3b091cda",
       "version": 1,
       "uri": "https://api.notifications.service.gov.uk/v2/templates/bfb50d92-100d-4b8b-b559-14fa3b091cda"
    }
}
```

Otherwise the client will raise a `StatusCodeError`:
<table>
<thead>
<tr>
<th>`error.status_code`</th>
<th>`error.message`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>429</pre>
</td>
<td>
<pre>
[{
    "error": "TooManyRequestsError",
    "message": "Exceeded send limits (50) for today"
}]
</pre>
</td>
</tr>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[{
    "error": "BadRequestError",
    "message": "Can"t send to this recipient using a team-only API key"
]}
</pre>
</td>
</tr>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[{
    "error": "BadRequestError",
    "message": "Can"t send to this recipient when service is in trial mode
                - see https://www.notifications.service.gov.uk/trial-mode"
}]
</pre>
</td>
</tr>
</tbody>
</table>
</details>

### Email

```javascript
notifyClient.sendEmail(templateId, emailAddress);
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `Object`:

```javascript
{
    "id": "bfb50d92-100d-4b8b-b559-14fa3b091cda",
    "reference": None,
    "content": {
        "subject": "Licence renewal",
        "body": "Dear Bill, your licence is due for renewal on 3 January 2016.",
        "from_email": "the_service@gov.uk"
    },
    "uri": "https://api.notifications.service.gov.uk/v2/notifications/ceb50d92-100d-4b8b-b559-14fa3b091cd",
    "template": {
        "id": "ceb50d92-100d-4b8b-b559-14fa3b091cda",
        "version": 1,
        "uri": "https://api.notificaitons.service.gov.uk/service/your_service_id/templates/bfb50d92-100d-4b8b-b559-14fa3b091cda"
    }
}
```

Otherwise the client will raise a `StatusCodeError`:
<table>
<thead>
<tr>
<th>`error.status_code`</th>
<th>`error.message`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>429</pre>
</td>
<td>
<pre>
[{
    "error": "TooManyRequestsError",
    "message": "Exceeded send limits (50) for today"
}]
</pre>
</td>
</tr>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[{
    "error": "BadRequestError",
    "message": "Can"t send to this recipient using a team-only API key"
]}
</pre>
</td>
</tr>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[{
    "error": "BadRequestError",
    "message": "Can"t send to this recipient when service is in trial mode
                - see https://www.notifications.service.gov.uk/trial-mode"
}]
</pre>
</td>
</tr>
</tbody>
</table>
</details>


### Arguments


#### `templateId`

Find by clicking **API info** for the template you want to send.

#### `personalisation`

If a template has placeholders you need to provide their values. For example:

```javascript
personalisation={
    'first_name': 'Amala',
    'reference_number': '300241',
}
```

Otherwise the parameter can be omitted or `undefined` can be passed in its place.

#### `reference`

An optional identifier you generate if you don’t want to use Notify’s `id`. It can be used to identify a single  notification or a batch of notifications.

## Get the status of one message
```javascript
notifyClient.getNotificationById(notificationId)
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `Object`:

```javascript
{
    "id": "notify_id",
    "body": "Hello Foo",
    "subject": "null|email_subject",
    "reference": "client reference",
    "email_address": "email address",
    "phone_number": "phone number",
    "line_1": "full name of a person or company",
    "line_2": "123 The Street",
    "line_3": "Some Area",
    "line_4": "Some Town",
    "line_5": "Some county",
    "line_6": "Something else",
    "postcode": "postcode",
    "type": "sms|letter|email",
    "status": "current status",
    "template": {
        "version": 1,
        "id": 1,
        "uri": "/template/{id}/{version}"
     },
    "created_at": "created at",
    "sent_at": "sent to provider at",
}
```

Otherwise the client will raise a `StatusCodeError`:
<table>
<thead>
<tr>
<th>`error.status_code`</th>
<th>`error.message`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>404</pre>
</td>
<td>
<pre>
[{
    "error": "NoResultFound",
    "message": "No result found"
}]
</pre>
</td>
</tr>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[{
    "error": "ValidationError",
    "message": "id is not a valid UUID"
}]
</pre>
</td>
</tr>
</tbody>
</table>
</details>

## Get the status of all messages
```javascript
notifyClient.getNotifications(templateType, status, reference, olderThan)
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `Object`:

```javascript
{ "notifications":
    [{
        "id": "notify_id",
        "reference": "client reference",
        "email_address": "email address",
        "phone_number": "phone number",
        "line_1": "full name of a person or company",
        "line_2": "123 The Street",
        "line_3": "Some Area",
        "line_4": "Some Town",
        "line_5": "Some county",
        "line_6": "Something else",
        "postcode": "postcode",
        "type": "sms | letter | email",
        "status": sending | delivered | permanent-failure | temporary-failure | technical-failure
        "template": {
            "version": 1,
          "id": 1,
          "uri": "/template/{id}/{version}"
       },
       "created_at": "created at",
       "sent_at": "sent to provider at",
    },
    …
  ],
  "links": {
     "current": "/notifications?template_type=sms&status=delivered",
     "next": "/notifications?other_than=last_id_in_list&template_type=sms&status=delivered"
  }
}
```

Otherwise the client will raise a `StatusCodeError`:
<table>
<thead>
<tr>
<th>`error.status_code`</th>
<th>`error.message`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[{
    'error': 'ValidationError',
    'message': 'bad status is not one of [created, sending, delivered, pending, failed, technical-failure, temporary-failure, permanent-failure]'
}]
</pre>
</td>
</tr>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[{
    "error": "ValidationError",
    "message": "Apple is not one of [sms, email, letter]"
}]
</pre>
</td>
</tr>
</tbody>
</table>
</details>

### Arguments

#### `templateType`

If omitted all messages are returned. Otherwise you can filter by:

* `email`
* `sms`
* `letter`


#### `status`

If omitted all messages are returned. Otherwise you can filter by:

* `sending` - the message is queued to be sent by the provider.
* `delivered` - the message was successfully delivered.
* `failed` - this will return all failure statuses `permanent-failure`, `temporary-failure` and `technical-failure`.
* `permanent-failure` - the provider was unable to deliver message, email or phone number does not exist; remove this recipient from your list.
* `temporary-failure` - the provider was unable to deliver message, email box was full or the phone was turned off; you can try to send the message again.
* `technical-failure` - Notify had a technical failure; you can try to send the message again.

#### `reference`


This is the `reference` you gave at the time of sending the notification. This can be omitted to ignore the filter.

#### `olderThan`

If omitted all messages are returned. Otherwise you can filter to retrieve all notifications older than the given notification `id`.
