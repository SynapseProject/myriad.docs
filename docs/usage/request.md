# myriAD Request Object

The code behind myriAD receives a JSON object as its input.  Whether this is passed in directly from the requestor (raw search), or constructed internally (search by object type), an LdapRequest object is created and processed by the software.  Below is the layout of the LdapRequest object and a description of each field.

````json
{
    "objectType": "ObjectTypeEnum",
    "domain": "DomainMappingKey",
    "searchValue": "SearchValue",
    "searchBase": "SearchBase",
    "searchScope": "All, One or Base",
    "maxResults": 10000,
    "nextToken": "Base64 Encoded String To Continue Paged Search",
    "wildcardSearch": false,
    "attributes": [
        "attribute001",
        "attribute002",
        "attribute003"
    ],
    "config": {
        "server": "ServerName",
        "port": 636,
        "ssl": true,
        "username": "MyUserName",
        "password": "MyEncryptedOrPlaintextPassword",
        "maxRetries": 0,
        "maxPageSize": 512,
        "followReferrals": false,
        "returnTypes": {
            "attribute001": "StringArray",
            "attribute002": "Guid",
            "attribute003": "Bytes"
        }
    },
    "crypto": {
        "text": "PlainTextPassword",
        "iv": "InitVector",
        "salt": "SaltValue",
        "passphrase": "Passphrase"
    },
    "ping": "Echo or NoEcho"
}
````

| Json Path | Required | Description
| --------- | -------- | -----------
| objectType | No | Type of object to search for.  Leave blank when using a raw LDAP Search Filter.  See [Object Types](#object-types) for valid values.
| domain | No | The domain mapping key or config profile that should be used to perform the search (see [LDAP Configuration](../install/aws.md#ldap-configuration) for details.)
| searchValue | Yes | The value to search on.  This should be the LDAP Search Filter for raw LDAP searches, or the identity of the object.  See [Identities](#identities) for more details.  The following [special characters](#reserved-search-characters) will need to be escaped: \ ( ) * and NULL.
| searchBase | No | The fully qualified domain name of the OU from where the search should being.  (Defaults to RootDSE).
| searchScope | No | Specifies the depth of the search.  **All** = Base Object and All Entries in its subtree, **One** = Base Object and immediate subordinates of the base object.  **Base** = Base Object only.  (Default Value = **All**)
| maxResults | No | The maximum number of records to return.  If more record exist, the "nextToken" attribute will be returned which allows for another search from the last record retrieved.  (See [Paged Searching](#paged-searching) For More Details)
| nextToken | No | A Base64 encoded token that allows for paged searching.  Used to return next set of records that still exist after the maxResults amount have been retrieved. (See [Paged Searching](#paged-searching) For More Details)
| wildcardSearch | No | Explicitly tells MyriAD that you are performing a wildcard search.  This is only really needed when you are searching for a DistinguishedName that also contains an asterik as part of the FQDN, thus telling MyriAD to treat the asterisk as the character and NOT as a wildcard.
| attributes | No | A list of attribute names to return with each found record.  If no list is provided, all attributes will be returned.  If an empty list is provided, no attributes will be returned.
| config > server | No | The server this request should connect to.
| config > port | No | The port this request should connect to.  Will default to 389 or 636 (depending on ssl flag) if no values provided.
| config > ssl | No | Boolean value on whether to connect to the server using a secure socket. 
| config > username | No | The username used to connect to the LDAP server.
| config > password | No | The password (encrypted or plaintext) ot use to connect to the LDAP Server.
| config > maxRetries | No | The number of times to retry reconnecting to the server before stopping. (Default = 0, No Retries)
| config > maxPageSize | No | This tells MyriAD how many results to pull back for each search it performs up to the "maxResults".  This affects the internal retrieval of results and is used mostly for performance tuning, not to control the number of records returned.  (Default = 512)
| config > followReferrals | No | Indicates whether or not to follow LDAP referrals when retrieving records.  This is used when records exist, but might not exist on the server you are attached to.  This does cause performance issues, so use it only if you know you need to use it.  (Default = False)
| config > returnTypes | No | Attributes default to returning as a single string.  Well known attributes will be returned as appropriate values.  The "returnTypes" section of the config specifies any attributes that should be returned as an object other than string.
| crypto > text | No | When a value is specified here, all other values are ignored and myriAD will perform an encryption of the value specified here.
| crypto > iv | No | The Init Vector to use when encrypting the value.
| crypto > salt | No | The Salt Value to use when encrypting the value.
| crypto > passphrase | No | The Passphrase to use when encrypting the value.
| ping | No | This is a way to test connectivity to MyriAD.  It returns a "Hello" message with the current version of MyriAD.

**NOTE :** The "config" and "crypto" sections will pull data from the default configuration and/or the domain specific configuration when values are not provided.  

### Object Types

Below is a list of supported object types : 

| Object Type | Returns
| ----------- | -------
| User | User and Contact Objects
| Group
| OrganizationalUnit
| Ou | Shorthand for OrganizationalUnit
| Contact | Contact Objects Only
| PrintQueue
| Printer | Same as PrinterQueue
| Computer
| Volume | Shared Folders
| Domain
| DomainController
| Dn | Shorthand for DistinguishedName
| DistinguishedName

### Identities

When searching based on object types, myriAD expects an identity in the searchValue field.  This identity will be search for against one or more attributes to find the matching records.  

All object types can be retrived by their **objectGUID**, **objectSID** or **Distinguished Name**.  Below is a list of other attributes searched based on each object type: 

| Object Type | Attributes Searched
| ----------- | -------------------
| User | cn, name, userPrincipalName, sAMAccountName
| Group | cn, name, sAMAccountName
| OrganizationalUnit | ou, name
| Ou | ou, name
| Contact | cn, name
| PrintQueue | cn, name
| Printer | cn, name
| Computer | cn, name, sAMAccountName
| Volume | cn, name
| Domain | name
| DomainController | cn, name, sAMAccountName
| Dn | distinguishedName
| DistinguishedName | distinguishedName

### Reserved Search Characters

The following characters will need to be escaped in the searchValue property to ensure proper a LDAP search occurs :

| Character | Description | Escape To | Example | Value In LDAP
| --------- | ----------- | --------- | ------- | -------------
| \ | Backslash | \5C  | (cn=Waguespack\5C, Guy) | Waguespack\, Guy
| ( | Open Parenthesis | \28 | (cn=Joe Blow \28Contractor\29) | Joe Blow (Contractor)
| ) | Close Parenthesis | \29 | (cn=Joe Blow \28Contractor\29) | Joe Blow (Contractor)
| * | Asterisk | \2A | (cn=Q*Bert) | Q*Bert (where the asterisk is a literal character in the value, not a wildcard search)
| NULL | Null Value | \00 | ??? | ???

Please note, that passing the filter string as a JSON Body, any backslashes will have to also be escaped to properly format the JSON, so examples of this would be : 

```json
{
	"searchValue": "(distinguishedName=CN=Waguespack\\5C, Guy \\28Contractor\\29,OU=Users,DC=sandbox,DC=local"
}

{
	"searchValue": "(distinguishedName=CN=Q\\2ABert,OU=Users,DC=sandbox,DC=local"
}
```

However, when searching by object type, either via a JSON Post or the HTTP Get URL, MyriAD will escape the characters for you.

```json
Request : 

{
	"objectType": "User",
	"searchValue": "CN=Waguespack\\, Guy (Contractor),OU=Users,DC=sandbox,DC=local",
    "attributes": [
        "cn"
    ]
}

Response : 
{
    "success": true,
    "server": "ldaps://sandbox.local:636",
    "searchBase": "DC=sandbox,DC=local",
    "searchFilter": "(&(objectCategory=User)(distinguishedName=CN=Waguespack\\5C, Guy \\28Contractor\\29,OU=Users,DC=sandbox,DC=local))",
    "records": [
        {
            "dn": "CN=Waguespack\\, Guy (Contractor),OU=Users,DC=sandbox,DC=local",
            "attributes": {
                "cn": "Waguespack, Guy (Contractor)"
            }
        }
    ]
}
```

```json
HTTP GET :
https://my.company.com/myriad/dn/CN=Waguespack%5C, Guy (Contractor),OU=Users,DC=sandbox,DC=local?attr=cn

Response : 
{
    "success": true,
    "server": "ldaps://sandbox.local:636",
    "searchBase": "DC=sandbox,DC=local",
    "searchFilter": "(distinguishedName=CN=Waguespack\\5C, Guy \\28Contractor\\29,OU=Users,DC=sandbox,DC=local)",
    "records": [
        {
            "dn": "CN=Waguespack\\, Guy (Contractor),OU=Users,DC=sandbox,DC=local",
            "attributes": {
                "cn": "Waguespack, Guy (Contractor)"
            }
        }
    ]
}
```

### Paged Searching

Beginning with version 1.1.23165.0 of MyriAD, the ability to specify the maximum number of records you want returned, and the ability to make a 2nd search passing in a "nextToken" field to retrieve more records starting from the last record retrieved was introduced.

This functionality, however, is HIGHLY dependant on the LDAP server's configuration as to whether it supports paged results (sometimes called LDAP Cookies).  In my testing observations, I've noticed that the token RARELY survives the LDAP connection being closed.  On busy servers, it also seems to be very short-lived, as the settings on the server's "Cookie Pool" are usually set low.

When a token cannot be found, or the server does not support pagination, the error below will appear.

```json
{
  "success": false,
  "server": "ldaps://sandbox.local:636",
  "searchBase": "OU=TooManyGroups,OU=Testing,DC=sandbox,DC=local",
  "searchFilter": "(&(objectCategory=Group)(|(cn=*)(name=*)(sAMAccountName=*)))",
  "message": "Unavailable Critical Extension",
  "totalRecords": 0
}
```