# myriAD Security

Optional security measures can be added to the myriAD deployment to control who has access to the API and how often they can call it.

## AWS API Keys and Usage Plans

myriAD is secured in the Amazon Web Services (AWS) implementation using [API Keys and Usage Plans](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html).

To access an instance of myriAD that is secured by an API Key, you must provide the following HTTP Header : 

````
x-api-key : <API KEY VALUE>
````

Internally, the key will be tied to an AWS Usage Plan that can limit how frequently the API Endpoint can be called.

## Password Encryption

As part of myriAD's configuration and usage, passwords are needed to connect to the LDAP Server(s) specified.  These passwords are provided either in the request itself, in the [config section](request.md), or in one or more environment variables. 

The passwords CAN be plain text, but it is HIGHLY recommended that you encrypt these passwords.   MyriAD uses Rijndael encrytion methods to keep passwords safe.  

The easiest way to encrypt a password is to call the search method passing in only the [crytpo](request.md) section.   If only the "text" field is provided the password will be encrypted with the IV, Salt and Passphrase values for this instance of myriAD.    If you wish to use other values for these items, pass them in as well (but then you MUST include them with each request containing using that password).

See [Examples](usage.md#examples) for, well, an example.  :)