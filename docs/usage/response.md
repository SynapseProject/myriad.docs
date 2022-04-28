# myriAD Response Object

myriAD responds with a single LdapResponse JSON object for each request it receives, even for encryption requests.   Below is the layout of the response as well as a description of each field.

````json
{
    "success": true,
    "server": "LDAP Server Connected To",
    "searchBase": "Search Base Used For Query",
    "searchFilter": "The Raw LDAP Search Filter Used For Query",
    "message": "Error Message or Encrypted Value of Password",
    "records": [
        {
            "dn": "DistinguishedName,
            "attributes": {
                "attr001": "value",
                "attr002": [
                    "val001",
                    "val002"
                ]
            }
        }
    ]
}
````

| Json Path | Description
| --------- | -----------
| success | Boolean Value indicating whether the action was executed sucessfully.
| server | The LDAP Server that the query was sent to.
| searchBase | The Search Base the query was executed against.
| searchFilter | The raw LDAP Search Filter used for the query.
| message | When success is false, the error message.  When query was an encrpytion request, this field will contained the encrypted value.
| records | A list of Zero or more records that are the result of the query.
| records > dn | The DistinguisedName of the LDAP record.  This will ALWAYS be returned for every record.
| records > attributes | A Key/Value dictionary of attributes requested to be returned with each object.   The "value" defaults to a single string unless specified in the request, configured as a default return type or is a [well known return type](#well-known-return-types)


## Return Types

By default, all attributes are returned as a single string value, with the exception of a few [well known return types](#well-known-return-types) and other attributes specified in the RETURN_VALUES environment variable, or in the request itself.

Below is a list of each possible return type available.

| Type | Value
| ---- | -----
| String | A single string value.
| StringArray | An array of string values.
| Bytes | A single HEX value string representing a byte array (starts with 0x)
| BytesArray | An array of HEX value string, each representing a byte array (starting with 0x)
| Guid | A string in GUID format (Ex: 027a7130-1234-4526-5678-d3f13f750de3)
| Sid | A string in SID format (Ex: S-1-5-21-1234567890-987654321-111222333-44556677)

## Well Known Return Types

By default, all attributes are returned as a single string.   These return types can be overriden in the request, or by being specified in an environment variable.  The values below are "well-known" attributes that are NOT single string objects and thus default to the values specified.

| Attribute | Default Return Type
| --------- | -------------------
| objectClass | StringArray 
| managedObjects | StringArray 
| dSCorePropagationData | StringArray 
| objectGUID | Guid 
| objectSid | Sid 
| member | StringArray 
| memberOf | StringArray 
| proxyAddresses | StringArray 
| businessCategory | StringArray 
| otherHomePhone | StringArray 
| otherPager | StringArray 
| otherFacsimileTelephoneNumber | StringArray 
| otherMobile | StringArray 
| otherIpPhone | StringArray 
| secretary | StringArray 
| servicePrincipalName | StringArray 
| subRefs | StringArray 
| wellKnownObjects | StringArray 
| otherWellKnownObjects | StringArray 

## Recommended Return Types

In my usage of myriAD, I've found a few other attributes that are much more meaningful when returned as values other than a single string.  Below is the JSON object we use with our instance of myriAD.  This is located in the RETURN_TYPES environment variable.

````json
{
  "comment": "Bytes",
  "mS-DS-ConsistencyGuid": "Guid",
  "msExchArchiveGUID": "Guid",
  "msExchMailboxGuid": "Guid",
  "thumbnailPhoto": "Bytes",
  "directReports": "StringArray",
  "showInAddressBook": "StringArray",
  "msRTCSIP-UserPolicies": "BytesArray"
}
````