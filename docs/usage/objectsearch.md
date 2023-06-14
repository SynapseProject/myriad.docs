# myriAD - Search By Object Type

myriAD makes it easier to search for records based on object type, by using a simple GET query with optional query parameters, instaed of having to construct an LDAP Search Filter each and every time.

## Request Format

The general format for a request by object type is : 

https://{myriAD_URL}/{myriAD_Stage}/[ObjectType](request.md#object-types)/[ObjectIdentity](request.md#identities)

Optionally, you can include the following query paramters after the URL to specify how and what data is returned : 

| Query Parameter | Example | Description
| --------------- | ------- | -----------
| attr | .../myriad/user/myuserid?attr=cn&attr=objectGUID | Indicates which attributes to return for each record, one attribute per query parameter, repeated.
| domain | .../myriad/user/OBJECT_GUID?domain=SB1 | Specifies the domain mapping or domain config to use to perform the search.  Primarily used for search by ObjectGUID or SecurityIdentifier, since domain can be included in the path for all other object types.
| maxPageSize | .../myriad/user/*?maxPageSize=1000 | This value is used internally by MyriAD to set the "per call" amount of records to return.   MyriAD will do internal pagination of results until it reaches "maxRecords" number of records.  This value is more for performance tuning and should rarely be used.
| maxResults | .../myriad/user/*?maxResults=1000 | This value indicates the maximum number of records to return with this search.  If more records exist, the "nextToken" value will be returned with the resultset for use in future calls.   (See [Paged Searching](request.md#paged-searching) For More Details on Pagination)
| nextToken | .../myriad/user/*?maxResults=1000&nextToken=xxxxxxxx | The nextToken value is used in pagination to return the next set of records.  (See [Paged Searching](request.md#paged-searching) For More Details on Pagination)
| searchScope | .../myriad/user/JohnSmith?searchBase=MyOUStructure&searchScope=One | Used in conjunction with "searchBase", this tells MyriAD "how deep" to search for the records.  (**All** = SearchBase and all subordiante records under it, **One** = SearchBase and only direct suboridnae records, **Base** = SearchBase object only.)  Default = All.
| searchBase | .../myriad/user/JohnSmith?searchBase=MyOUStructure | Indicates where in the directory structure to being the search.  

## Example Calls

**Return Group with ALL attributes**

https://.../myriad/group/MyGroupName

**Return Computer with NO attributes**

https://.../myriad/computer/MyComputerName?attr=

**Return Printer with 3 attributes**

https://.../myriad/printer/MyPrinterName?attr=cn&attr=name&attr=description

**Return User From Domain SB1**

https://.../myriad/user/SB1/MySamAccountName

https://.../myriad/user/MyObjectGUID?domain=SB1

https://.../myriad/user/MySecurityIdentifier?domain=SB1

**Return Only First 100 Records**

https://.../myriad/user/MyUsername?maxResults=100

**Return Users Who Are Dirctly Under A Specified OrganizationalUnit (OU)**

https://.../myriad/user/*?searchBase=OU=Managers,DC=sandbox,DC=local&searchScope=One

