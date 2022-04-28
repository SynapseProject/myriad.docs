# myriAD Security

Optional security measures can be added to the myriAD deployment to control who has access to the API and how often they can call it.

## AWS API Keys and Usage Plans

myriAD is secured in the Amazon Web Services (AWS) implementation using [API Keys and Usage Plans](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html).

To access an instance of myriAD that is secured by an API Key, you must provide the following HTTP Header : 

````
x-api-key : <API KEY VALUE>
````

Internally, the key will be tied to an AWS Usage Plan that can limit how frequently the API Endpoint can be called.