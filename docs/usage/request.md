# myriad Request Object

The code behind myriAD receives a JSON object as its input.  Whether this is passed in directly from the requestor (raw search), or constructed internally (search by object type), an LdapRequest object is created and processed by the software.  Below is the layout of the LdapRequest object and a description of each field.

````json
{
    "objectType": "ObjectTypeEnum",
    "searchValue": "SearchValue",
    "searchBase": "SearchBase",
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
        "maxResults": 1000,
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
    }
}
````

| Json Path | Required | Description
| --------- | -------- | -----------
| objectType | No | Type of object to search for.  Leave blank when using a raw LDAP Search Filter.  See [Object Types](#object-types) for valid values.
| searchValue | Yes | The value to search on.  This should be the LDAP Search Filter for raw LDAP searches, or the identity of the object.  See [Identities](#identities) for more details.
| searchBase | No | The fully qualified domain name of the OU from where the search should being.  (Defaults to RootDSE).
| attributes | No | A list of attribute names to return with each found record.  If no list is provided, all attributes will be returned.  If an empty list is provided, no attributes will be returned.
| config > server | No | The server this request should connect to.
| config > port | No | The port this request should connect to.  Will default to 389 or 636 (depending on ssl flag) if no values provided.
| config > ssl | No | Boolean value on whether to connect to the server using a secure socket. 
| config > username | No | The username used to connect to the LDAP server.
| config > password | No | The password (encrypted or plaintext) ot use to connect to the LDAP Server.
| config > returnTypes | No | Attributes default to returning as a single string.  Well known attributes will be returned as appropriate values.  The "returnTypes" section of the config specifies any attributes that should be returned as an object other than string.
| crypto > text | No | When a value is specified here, all other values are ignored and myriAD will perform an encryption of the value specified here.
| crypto > iv | No | The Init Vector to use when encrypting the value.
| crypto > salt | No | The Salt Value to use when encrypting the value.
| crypto > passphrase | No | The Passphrase to use when encrypting the value.

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

### Identities

When searching based on object types, myriAD expects an identity in the searchValue field.  This identity will be search for against one or more attributes to find the matching records.  

All object types can be retrived by their **objectGUID**, **objectSID** or **Distinguished Name**.  Below is a list of other attributes searched base on each object type: 

| Object Type | Searched Attributes
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

