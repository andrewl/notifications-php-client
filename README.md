# GOV.UK Notify PHP client

#### PSR-7 HTTP

The Notify PHP Client is based on a PSR-7 HTTP model. You therefore need to pick your preferred HTTP Client library to use.

We will show examples here using the Guzzle v6 Adapter.

Setup instructions are also available for [Curl](docs/curl-client-setup.md) and [Guzzle v5](docs/guzzle5-client-setup.md).

## Installing

The Notify PHP Client can be installed with [Composer](https://getcomposer.org/). Run this command:

```sh
composer require php-http/guzzle6-adapter alphagov/notifications-php-client
```

## Getting started

Assuming you’ve installed the package via Composer, the Notify PHP Client will be available via the autoloader.

Create a (Guzzle v6 based) instance of the Client using:

```php
$notifyClient = new \Alphagov\Notifications\Client([
    'apiKey' => '{your api key}',
    'httpClient' => new \Http\Adapter\Guzzle6\Client
]);
```

Generate an API key by logging in to [GOV.UK Notify](https://www.notifications.service.gov.uk) and going to the **API integration** page.

## Send messages

### Text message

The method signature is:
```php
sendSms( $phoneNumber, $templateId, array $personalisation = array(), $reference = '' )
```

An example request would look like:

```php
try {

    $response = $notifyClient->sendSms( '+447777111222', 'df10a23e-2c6d-4ea5-87fb-82e520cbf93a', [
        'name' => 'Betty Smith',
        'dob'  => '12 July 1968'
    ]);

} catch (NotifyException $e){}
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `array`:

```php
[
    "id" => "bfb50d92-100d-4b8b-b559-14fa3b091cda",
    "reference" => None,
    "content" => [
        "body" => "Some words",
        "from_number" => "40604"
    ],
    "uri" => "https =>//api.notifications.service.gov.uk/v2/notifications/ceb50d92-100d-4b8b-b559-14fa3b091cd",
    "template" => [
        "id" => "ceb50d92-100d-4b8b-b559-14fa3b091cda",
       "version" => 1,
       "uri" => "https://api.notifications.service.gov.uk/v2/templates/bfb50d92-100d-4b8b-b559-14fa3b091cda"
    ]
]
```

Otherwise the client will raise a ``Alphagov\Notifications\Exception\NotifyException``:
<table>
<thead>
<tr>
<th>`error["status_code"]`</th>
<th>`error["message"]`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>429</pre>
</td>
<td>
<pre>
[[
    "error"=> "RateLimitError",
    "message"=> "Exceeded rate limit for key type TEAM of 10 requests per 10 seconds"
]]
</pre>
</td>
</tr>
<tr>
<td>
<pre>429</pre>
</td>
<td>
<pre>
[
  [
    "error" => "TooManyRequestsError",
    "message" => "Exceeded send limits (50) for today"
  ]
]
</pre>
</td>
</tr>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[
  [
    "error" => "BadRequestError",
    "message" => "Can"t send to this recipient using a team-only API key"
  ]
]
</pre>
</td>
</tr>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[
  [
    "error" => "BadRequestError",
    "message" => "Can"t send to this recipient when service is in trial mode
                - see https://www.notifications.service.gov.uk/trial-mode"
  ]
]
</pre>
</td>
</tr>
</tbody>
</table>
</details>

### Email

The method signature is:
```php
sendEmail( $emailAddress, $templateId, array $personalisation = array(), $reference = '' )
```

An example request would look like:

```php
try {

    $response = $notifyClient->sendEmail( 'betty@exmple.com', 'df10a23e-2c0d-4ea5-87fb-82e520cbf93c', [
        'name' => 'Betty Smith',
        'dob'  => '12 July 1968'
    ]);

} catch (NotifyException $e){}
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `array`:

```php
[
    "id" => "bfb50d92-100d-4b8b-b559-14fa3b091cda",
    "reference" => None,
    "content" => [
        "subject" => "Licence renewal",
        "body" => "Dear Bill, your licence is due for renewal on 3 January 2016.",
        "from_email" => "the_service@gov.uk"
    ],
    "uri" => "https://api.notifications.service.gov.uk/v2/notifications/ceb50d92-100d-4b8b-b559-14fa3b091cd",
    "template" => [
        "id" => "ceb50d92-100d-4b8b-b559-14fa3b091cda",
        "version" => 1,
        "uri" => "https://api.notificaitons.service.gov.uk/service/your_service_id/templates/bfb50d92-100d-4b8b-b559-14fa3b091cda"
    ]
]
```

Otherwise the client will raise a ``Alphagov\Notifications\Exception\NotifyException``:
<table>
<thead>
<tr>
<th>`error["status_code"]`</th>
<th>`error["message"]`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>429</pre>
</td>
<td>
<pre>
[
  [
    "error" => "RateLimitError",
    "message" => "Exceeded rate limit for key type TEAM of 10 requests per 10 seconds"
}]
</pre>
</td>
</tr>
<tr>
<td>
<pre>429</pre>
</td>
<td>
<pre>
[
  [
    "error" => "TooManyRequestsError",
    "message" => "Exceeded send limits (50) for today"
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
[
  [
    "error" => "BadRequestError",
    "message" => "Can"t send to this recipient using a team-only API key"
  ]
]
</pre>
</td>
</tr>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[
  [
    "error" => "BadRequestError",
    "message" => "Can"t send to this recipient when service is in trial mode
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

```php
personalisation = [
    'name' => 'Betty Smith',
    'dob'  => '12 July 1968'
]
```

Otherwise the parameter can be omitted.

#### `reference`

An optional identifier you generate if you don’t want to use Notify’s `id`. It can be used to identify a single  notification or a batch of notifications.

## Get the status of one message

The method signature is:
```php
getNotification( $notificationId )
```

An example request would look like:

```php
try {

    $response = $notifyClient->getNotification( 'c32e9c89-a423-42d2-85b7-a21cd4486a2a' );

} catch (NotifyException $e){}
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `array `:

```php
[
    "id" => "notify_id",
    "body" => "Hello Foo",
    "subject" => "null|email_subject",
    "reference" => "client reference",
    "email_address" => "email address",
    "phone_number" => "phone number",
    "line_1" => "full name of a person or company",
    "line_2" => "123 The Street",
    "line_3" => "Some Area",
    "line_4" => "Some Town",
    "line_5" => "Some county",
    "line_6" => "Something else",
    "postcode" => "postcode",
    "type" => "sms|letter|email",
    "status" => "current status",
    "template" => [
        "version" => 1,
        "id" => 1,
        "uri" => "/template/{id}/{version}"
     ],
    "created_at" => "created at",
    "sent_at" => "sent to provider at",
]
```

Otherwise the client will raise a ``Alphagov\Notifications\Exception\NotifyException``:
<table>
<thead>
<tr>
<th>`error["status_code"]`</th>
<th>`error["message"]`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>404</pre>
</td>
<td>
<pre>
[
  [
    "error" => "NoResultFound",
    "message" => "No result found"
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
[
  [
    "error" => "ValidationError",
    "message" => "id is not a valid UUID"
}]
</pre>
</td>
</tr>
</tbody>
</table>
</details>

## Get the status of all messages
The method signature is:
```php
listNotifications( array $filters = array() )
```

An example request would look like:

```php
    $response = $notifyClient->listNotifications([
        'older_than' => 'c32e9c89-a423-42d2-85b7-a21cd4486a2a',
        'reference' => 'weekly-reminders',
        'status' => 'delivered',
        'template_type' => 'sms'
    ]);
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `array`:

```php
[
    "notifications":
    [
            "id" => "notify_id",
            "reference" => "client reference",
            "email_address" => "email address",
            "phone_number" => "phone number",
            "line_1" => "full name of a person or company",
            "line_2" => "123 The Street",
            "line_3" => "Some Area",
            "line_4" => "Some Town",
            "line_5" => "Some county",
            "line_6" => "Something else",
            "postcode" => "postcode",
            "type" => "sms | letter | email",
            "status" => sending | delivered | permanent-failure | temporary-failure | technical-failure
            "template" => [
            "version" => 1,
            "id" => 1,
            "uri" => "/template/{id}/{version}"
        ],
        "created_at" => "created at",
        "sent_at" => "sent to provider at",
        ],
        …
  ],
  "links" => [
     "current" => "/notifications?template_type=sms&status=delivered",
     "next" => "/notifications?other_than=last_id_in_list&template_type=sms&status=delivered"
  ]
]
```

Otherwise the client will raise a ``Alphagov\Notifications\Exception\NotifyException``:
<table>
<thead>
<tr>
<th>`error["status_code"]`</th>
<th>`error["message"]`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[
  [
    'error' => 'ValidationError',
    'message' => 'bad status is not one of [created, sending, delivered, pending, failed, technical-failure, temporary-failure, permanent-failure]'
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
[
  [
    "error" => "ValidationError",
    "message" => "Apple is not one of [sms, email, letter]"
}]
</pre>
</td>
</tr>
</tbody>
</table>
</details>

### Arguments

#### `older_than`

If omitted all messages are returned. Otherwise you can filter to retrieve all notifications older than the given notification `id`.

#### `template_type`

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



## Get a template by ID

```php
    $response = $notifyClient->getTemplate( 'c32e9c89-a423-42d2-85b7-a21cd4486a2a' );
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `array`:

```php
{
    "id" => "template_id",
    "type" => "sms|email|letter",
    "created_at" => "created at",
    "updated_at" => "updated at",
    "version" => "version",
    "created_by" => "someone@example.com",
    "body" => "body",
    "subject" => "null|email_subject"
}
```

Otherwise the client will return an error `err`:
<table>
<thead>
<tr>
<th>`error["status_code"]`</th>
<th>`error["errors"]`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>404</pre>
</td>
<td>
<pre>
[
  [
    "error" => "NoResultFound",
    "message" => "No result found"
  ]
]
</pre>
</td>
</tr>
</tbody>
</table>
</details>

### Arguments


#### `templateId`

Find by clicking **API info** for the template you want to send.

## Get a template by ID and version

```php
    $response = $notifyClient->getTemplateVersion( 'c32e9c89-a423-42d2-85b7-a21cd4486a2a', 1 );
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `array`:

```php
[
    "id" => "template_id",
    "type" => "sms|email|letter",
    "created_at" => "created at",
    "updated_at" => "updated at",
    "version" => "version",
    "created_by" => "someone@example.com",
    "body" => "body",
    "subject" => "null|email_subject"
]
```

Otherwise the client will return an error `err`:
<table>
<thead>
<tr>
<th>`error["status_code"]`</th>
<th>`error["errors"]`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>404</pre>
</td>
<td>
<pre>
[
  [
    "error" => "NoResultFound",
    "message" => "No result found"
  ]
]
</pre>
</td>
</tr>
</tbody>
</table>
</details>

### Arguments

#### `templateId`

Find by clicking **API info** for the template you want to send.

#### `version`

The version number of the template

## Get all templates

```php
    $this->getAllTemplates(
      $template_type  // optional
    );
```
_This will return the latest version for each template_

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `array`:

```php
[
    "templates"  => [
        [
            "id" => "template_id",
            "type" => "sms|email|letter",
            "created_at" => "created at",
            "updated_at" => "updated at",
            "version" => "version",
            "created_by" => "someone@example.com",
            "body" => "body",
            "subject" => "null|email_subject"
        ],
        [
            ... another template
        ]
    ]
]
```

+If no templates exist for a template type or there no templates for a service, the `response` will be a Dictionary` with an empty `templates` list element:

```php
[
    "templates"  => []
]
```

</details>

### Arguments

#### `$templateType`

If omitted all messages are returned. Otherwise you can filter by:

* `email`
* `sms`
* `letter`


## Generate a preview template

```php
    $personalisation = [ "foo" => "bar" ];
    $this->previewTemplate( $templateId, $personalisation );
```

<details>
<summary>
Response
</summary>

If the request is successful, `response` will be an `array`:

```php
[
    "id" => "notify_id",
    "type" => "sms|email|letter",
    "version" => "version",
    "body" => "Hello bar" // with substitution values,
    "subject" => "null|email_subject"
]
```

Otherwise the client will return an error `err`:
<table>
<thead>
<tr>
<th>`error["status_code"]`</th>
<th>`error["errors"]`</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<pre>400</pre>
</td>
<td>
<pre>
[
  [
    "error" => "BadRequestError",
    "message" => "Missing personalisation => [name]"
  ]
]
</pre>
</td>
</tr>
<tr>
<td>
<pre>404</pre>
</td>
<td>
<pre>
[
  [
    "error" => "NoResultFound",
    "message" => "No result found"
  ]
]
</pre>
</td>
</tr>
</tbody>
</table>
</details>

### Arguments


#### `$templateId`

Find by clicking **API info** for the template you want to send.

#### `$personalisation`

If a template has placeholders you need to provide their values. For example:

```php
$personalisation = [
    'first_name' => 'Amala',
    'reference_number' => '300241',
];
```

Otherwise the parameter can be omitted or `null` can be passed in its place.


## Development

#### Tests

There are unit and integration tests that can be run to test functionality of the client.

To run the unit tests:

```sh
vendor/bin/phpspec run spec/unit/ --format=pretty --verbose
```

To run the integration tests:

```sh
vendor/bin/phpspec run spec/integration/ --format=pretty --verbose
```

To run both sets of tests:

```sh
vendor/bin/phpspec run --format=pretty
```

## License

The Notify PHP Client is released under the MIT license, a copy of which can be found in [LICENSE](LICENSE.txt).
