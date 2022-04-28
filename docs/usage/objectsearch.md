# myriAD - Search By Object Type

myriAD makes it easier to search for records based on object type, by using a simple GET query with optional query parameters, instaed of having to construct an LDAP Search Filter each and every time.

## Request Format

The general format for a request by object type is : 

https://{myriAD_URL}/{myriAD_Stage}/[ObjectType](request.md#object-types)/[ObjectIdentity](request.md#identities)

Optionally, you can include the following query paramters after the URL to specify how and what data is returned : 

| Query Parameter | Example | Description
| --------------- | ------- | -----------
| attr | .../myriad/user/myuserid?attr=cn&attr=objectGUID | Indicates which attributes to return for each record, one attribute per query parameter, repeated.


## Example Calls

**Return Group with ALL attributes**

https://.../myriad/group/MyGroupName

**Return Computer with NO attributes**

https://.../myriad/computer/MyComputerName?attr=

**Return Printer with 3 attributes**

https://.../myriad/printer/MyPrinterName?attr=cn&attr=name&attr=description
