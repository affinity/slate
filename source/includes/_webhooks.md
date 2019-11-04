# Webhooks

Webhooks allow you to be notified of events that happen on your Affinity instance. For example, your app can be notified when a list is created, a field value is updated, a person is deleted, and more. These events will fire immediately after the corresponding action takes place.

## The webhook subscription resource

> Example Response

```json
{
  "id": 1234,
  "webhook_url": "https://hooks.example.com/webhook",
  "subscriptions": [
    "list.created",
    "person.created",
    "organization.created",
    "opportunity.created"
  ]
}
```

Each webhook subscription object has a unique `id`. It also has a `webhook_url` and `subscriptions` associated with it.

| Attribute     | Type     | Description                                                                                                                                                                                                           |
| ------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id            | integer  | The unique identifier of the webhook subscription object.                                                                                                                                                             |
| webhook_url   | string   | The URL to which the webhooks are sent to.                                                                                                                                                                            |
| subscriptions | string[] | An array of webhook events that are enabled for that endpoint. An empty array indicates subscription to all webhook events. See [below](#supported-webhook-events) for the complete list of supported webhook events. |

## Supported Webhook Events

| Object Type  | Events                                                                 |
| ------------ | ---------------------------------------------------------------------- |
| List         | `list.created`, `list.updated`, `list.deleted`                         |
| List Entry   | `list_entry.created`, `list_entry.deleted`                             |
| Note         | `note.created`, `note.updated`, `note.deleted`                         |
| Field        | `field.created`, `field.updated`, `field.deleted`                      |
| Field Value  | `field_value.created`, `field_value.updated`, `field_value.deleted`    |
| Person       | `person.created`, `person.updated`, `person.deleted`                   |
| Organization | `organization.created`, `organization.updated`, `organization.deleted` |
| Opportunity  | `opportunity.created`, `opportunity.updated`, `opportunity.deleted`    |
| Entity File  | `file.created`, `file.deleted`                                         |

## Get all webhook subscriptions

> Example Request

```shell
curl "https://api.affinity.co/webhook" -u :<API-KEY>
```

> Example Response

```json
[
  {
    "id": 1234,
    "webhook_url": "https://hooks.example.com/webhook-create",
    "subscriptions": [
      "list.created",
      "person.created",
      "organization.created",
      "opportunity.created"
    ]
  },
  {
    "id": 2345,
    "webhook_url": "https://hooks.example.com/webhook-all",
    "subscriptions": []
  }
]
```

`GET /webhook`

Returns all of your organization's webhook subscriptions.

## Get a specific webhook subscription

> Example Request

```shell
curl "https://api.affinity.co/webhook/1234" -u :<API-KEY>
```

> Example Response

```json
{
  "id": 1234,
  "webhook_url": "https://hooks.example.com/webhook",
  "subscriptions": [
    "list.created",
    "person.created",
    "organization.created",
    "opportunity.created"
  ]
}
```

`GET /webhook/{webhook_subscription_id}`

Gets the details for a specific webhook subscription given the specified `webhook_subscription_id`.

### Path Parameters

| Parameter               | Type    | Required | Description                                                           |
| ----------------------- | ------- | -------- | --------------------------------------------------------------------- |
| webhook_subscription_id | integer | true     | The unique id of the webhook subscription that needs to be retrieved. |

### Returns

The webhook subscription object corresponding to the `webhook_subscription_id`.

## Create a new webhook subscription

> Example Request

```shell
curl "https://api.affinity.co/webhook/subscribe" \
  -u :<API-KEY> \
  -d webhook_url="https://hooks.example.com/webhook"
```

> Example Response

```json
{
  "id": 1234,
  "webhook_url": "https://hooks.example.com/webhook",
  "subscriptions": []
}
```

`POST /webhook/subscribe`

Creates a new webhook subscription with the supplied parameters. If the endpoint returns an invalid response, the webhook creation will fail.

**Note:**

There is currently a limit of 3 webhook subscriptions.

### Payload Parameters

| Parameter     | Type     | Required | Description                                                                                                                                                                                                                                          |
| ------------- | -------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| webhook_url   | string   | true     | The URL to which the webhooks will be sent to.                                                                                                                                                                                                       |
| subscriptions | string[] | false    | An array of webhook events that will be enabled for that endpoint. Leave out this parameter or pass an empty array to subscribe to all webhook events. You can find the complete list of supported webhook events [here](#supported-webhook-events). |

### Returns

The webhook subscription object that was just created from this successful request.

## Delete a specific webhook subscription

> Example Request

```shell
curl "https://api.affinity.co/webhook/1234" \
  -u :<API-KEY> \
  -X "DELETE"
```

> Example Response

```json
{ "success": true }
```

`DELETE /webhook/{webhook_subscription_id}`

Deletes a webhook subscription with a specified `webhook_subscription_id`.

### Path Parameters

| Parameter               | Type    | Required | Description                                                         |
| ----------------------- | ------- | -------- | ------------------------------------------------------------------- |
| webhook_subscription_id | integer | true     | The unique id of the webhook subscription that needs to be deleted. |

### Returns

`{success: true}`.

## Limitations

- The following triggers will not create webhook events:

  - Resources created/updated/deleted via our importer tool
  - Resources created/updated/deleted via Email Bot.
  - Organizations created automatically based on email/event interactions.
  - Field values created/updated/deleted in automated global organization fields (i.e. location, last round raised, etc.)

- There is a limit of three webhook subscriptions. If you have any questions at all regarding webhooks, please reach out to support@affinity.co.