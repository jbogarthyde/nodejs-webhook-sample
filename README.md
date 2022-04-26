# NodeJS Webhook Sample

A sample nodejs project to consume Everactive webhook messages. This sample application stores the messages in an in-memory array limiting the number of stored messages to the most recent ten messages received.

## Everactive Webhooks

Everactive provides near real-time sensor readings via a webhook subscription. See the [Everacrtive webhooks API overview](https://everactive-ds-docs.readme.io/reference/webhooks-overview) for more details of how to register a webhook with Everactive.

## Hosting Options

The sample application can be hosted anywhere a node application or docker container can be run. Two options are described in more detail below.

### Local Test (with Docker)

The sample application can be run locally with a tunnel to accept external incoming requests.

The included Dockerfile can be used to build and run the sample application locally:

```
docker build . -t everactive/webhook-sample
docker run --env API_KEY=secret_key -p 3005:3000 --name evrac-webhook-sample --rm everactive/webhook-sample
```

Check the webhook sample is working properly:

```
curl -v 'http://localhost:3005/healh'
curl -v -X POST 'http://localhost:3005' \
  -H 'x-api-key: secret_key' \
  -H 'Content-Type: application/json' \
  -d '{"keyA": "valueA", "keyB": "valueB"}'
curl -v 'http://localhost:3005' --header 'x-api-key: secret_key'
```

A tunneling solution such as [ngrock](https://ngrock.com) or [localtunnel](https://github.com/localtunnel/localtunnel) can be used to expose the running docker container to the outside world.

### Digital Ocean Deployment

As a convinence we have provided a button to deploy this sample project on Digital Ocean (an account is required).

When deploying an environment variable can be set, `API_KEY` to designate the `x-api-key` header that must be included to authorize requests.

[![Deploy to DO](https://www.deploytodo.com/do-btn-blue.svg)](https://cloud.digitalocean.com/apps/new?repo=https://github.com/everactive/nodejs-webhook-sample.git/tree/main)

## Webhook Registration

Once the application is running you must register the webhook endpoint with Evercative via the API. To register:

```
curl -v -X POST 'https://api.data.everactive.com/ds/v1/webhooks \
  -H `Authorization: Bearer {{access_token}}' \
  -H 'Content-Type: application/json' \
  -d '{"callbackUrl": "https://[your sample webhook url]", "eventType": "sensor_reading", "enabled": true, "headers": [{"key": "x-api-key", "value": "secret_key"}]}'
```

The request will return a webhook object including an ID value. Note the ID so you can delete the webhook subscription when done running the sample.

Before tearing down the sample webhook, you should delete the webhook subscription in Everactive:

```
curl -v -X DELETE 'https://api.data.everactive.com/ds/v1/webhooks/{{webhookId}}
```
