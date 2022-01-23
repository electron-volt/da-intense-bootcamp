# Routes, integrations,stages

## Stage

An API stage is a logical reference to a lifecycle state of your API (for example, `dev`, `prod`, `beta`, or `v2`). API stages are identified by their API ID and stage name, and they're included in the URL you use to invoke the API. Each stage is a named reference to a deployment of the API and is made available for client applications to call.

You can create a `$default` stage that is served from the base of your API's URL—for example, `https://{api_id}.execute-api.{region}.amazonaws.com/`. This is what we did in the previous lab.&#x20;

If we were to create `beta` and `prod` stages, we would invoke them with&#x20;

`https://{api_id}.execute-api.{region}.amazonaws.com/beta` \
`https://{api_id}.execute-api.{region}.amazonaws.com/prod`

### Deploying to a stage

A deployment is a snapshot of your API configuration. After you deploy an API to a stage, it’s available for clients to invoke. You must deploy an API for changes to take effect.&#x20;

## Routes

Routes direct incoming API requests to backend resources. Routes consist of two parts: an HTTP method and a resource path—for example, `GET /pets`. You can define specific HTTP methods for your route. Or, you can use the `ANY` method to match all methods that you haven't defined for a resource. You can create a `$default` route that acts as a catch-all for requests that don’t match any other routes.

### Routing API requests <a href="#http-api-develop-routes.evaluation" id="http-api-develop-routes.evaluation"></a>

When a client sends an API request, API Gateway first determines which [stage](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-stages.html) to route the request to.&#x20;

* If the request explicitly matches a stage, API Gateway sends the request to that stage.&#x20;
* If no stage fully matches the request, API Gateway sends the request to the `$default` stage.&#x20;
* If there's no `$default` stage, then the API returns `{"message":"Not Found"}`.

After selecting a stage, API Gateway selects a route. API Gateway selects the route with the most-specific match, using the following priorities:

1. Full match for a route and method.
2. Match for a route and method with a greedy path variable (`{proxy+}`).
3. The `$default` route.

A _greedy path variable_ catches all child resources of a route. To create a greedy path variable, add `+` to the variable name—for example, `{proxy+}`. The greedy path variable must be at the end of the resource path.

If no routes match a request, API Gateway returns `{"message":"Not Found"}` to the client.

## Integrations

_Integrations_ connect a route to backend resources. HTTP APIs support Lambda proxy, AWS service, and HTTP proxy integrations. For example, you can configure a `POST` request to the `/signup` route of your API to integrate with a Lambda function that handles signing up customers.

