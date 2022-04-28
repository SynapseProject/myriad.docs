# myriAD Usage

myriAD works by placing a REST call to the API interface.  Below is a list of each supported request type :

- **Password Encryption** : Returns an encrypted version of a password based on either the default encryption keys, or using the encryption keys passed in with the request.
- **Raw LDAP Search** : Executes a search against the LDAP server using an LDAP Search Filter using an HTTP POST.
- **Search By Object Type** : Executes a search for a specific LDAP Object Type (User, Group, etc...) using an HTTP GET.

## Request and Response Objects

Full details can be found on the [Request](request.md) and [Response](response.md) pages.

## Examples

### Encrypt Password

Plain text password is included in the body of the HTTP POST, under the crypto > text element.   The value returned in the message element can be used with this instance of myriAD anywhere a password is required (default config, config section of request, etc...)

[Request](request.md)

````json
Http Verb:  POST
Url :       https://{{apigateway}}/{{apigwystage}}/search
Body:
{
    "crypto": {
        "text": "HelloWorld"
    }
}
````

[Response](response.md)

````json
{
    "success": true,
    "message": "h9fgZk7oesoktn7kVrtx/Q=="
}
````

### Search Using LDAP Filter

Provies a raw LDAP Search Filter in the "searchValue" element.  Returns a list of matching records and the attributes requested.

[Request](request.md)

````json
Http Verb:  POST
Url :       https://{{apigateway}}/{{apigwystage}}/search
Body:
{
	"searchValue": "(&(objectCategory=User)(cn=Waguespack*))",
    "attributes": [
        "cn",
        "objectGUID"
    ]
}
````

[Response](response.md)

````json
{
    "success": true,
    "server": "ldaps://sb1.sandbox.com:636",
    "searchBase": "DC=sandbox,DC=com",
    "searchFilter": "(&(objectCategory=User)(cn=Waguespack*))",
    "records": [
        {
            "dn": "CN=Waguespack, Guy,OU=Users,DC=sandbox,DC=com",
            "attributes": {
                "cn": "Waguespack, Guy",
                "objectGUID": "abcdefgh-ijkl-mnop-qrst-uvwxyz000001"
            }
        },
        {
            "dn": "CN=Waguespack, John,OU=Users,DC=sandbox,DC=com",
            "attributes": {
                "cn": "Waguespack, John",
                "objectGUID": "abcdefgh-ijkl-mnop-qrst-uvwxyz000002"
            }
        },
        {
            "dn": "CN=Waguespack, Mark,OU=Users,DC=sandbox,DC=com",
            "attributes": {
                "cn": "Waguespack, Mark",
                "objectGUID": "abcdefgh-ijkl-mnop-qrst-uvwxyz000003"
            }
        }
    ]
}
````

### Search By Object Type (User)

Same request as above, except executed as an HTTP GET, with requested attributes passed in as query parameters.

[Request](request.md)

````json
Http Verb:  GET
Url :       https://{{apigateway}}/{{apigwystage}}/user/Waguespack*?attr=cn&attr=objectGUID
````

[Response](response.md)

````json
{
    "success": true,
    "server": "ldaps://sb1.sandbox.com:636",
    "searchBase": "DC=sandbox,DC=com",
    "searchFilter": "(&(objectCategory=User)(|(cn=Waguespack*)(name=Waguespack*)(sAMAccountName=Waguespack*)))",
    "records": [
        {
            "dn": "CN=Waguespack, Guy,OU=Users,DC=sandbox,DC=com",
            "attributes": {
                "cn": "Waguespack, Guy",
                "objectGUID": "abcdefgh-ijkl-mnop-qrst-uvwxyz000001"
            }
        },
        {
            "dn": "CN=Waguespack, John,OU=Users,DC=sandbox,DC=com",
            "attributes": {
                "cn": "Waguespack, John",
                "objectGUID": "abcdefgh-ijkl-mnop-qrst-uvwxyz000002"
            }
        },
        {
            "dn": "CN=Waguespack, Mark,OU=Users,DC=sandbox,DC=com",
            "attributes": {
                "cn": "Waguespack, Mark",
                "objectGUID": "abcdefgh-ijkl-mnop-qrst-uvwxyz000003"
            }
        }
    ]
}
````
