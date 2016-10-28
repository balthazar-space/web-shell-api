# fs 

## <small>Action</small> fs-path</code> 
__Return information about a path.__ 

| Consumes | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | The path to query.<br><small></small> |
| __fields__<br><small>IdentArray</small> | Fields that should be returned.<br><small></small> |


| Produces | Description |
|:---- |:---- |
| __filename__<br><small>FilenameString</small> | The cannonical name of the file<br><small>Return `null` if the file does not exist.</small> |
| __type__<br><small>MimeTypeString</small> | The MIME type of the file at this path<br><small>Return `"directory"` for directories, `"file"` for files of unknown type and `null` if the file does not exist.</small> |
| __can__<br><small>IdentArray</small> | FS actions that can be executed on this file<br><small>e.g.`["read","write"]`</small> |
| __size__<br><small>Integer</small> | Byte length of the file<br><small></small> |
| __ctime__<br><small>UnixTime</small> | Creation timestamp<br><small></small> |
| __mtime__<br><small>UnixTime</small> | Modification timestamp<br><small></small> |


## <small>Action</small> fs-info</code> 
__Return information about a file.__ 

Behaves the same as `fs-path` except it throws `not-found` if the file does not exist.

| Consumes | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | The file to query.<br><small></small> |
| __fields__<br><small>IdentArray</small> | Fields that should be returned.<br><small></small> |


| Produces | Description |
|:---- |:---- |
| __---__<br><small>Various</small> | requested PathInfo fields<br><small></small> |


## <small>Action</small> fs-read</code> 
__Read the content of a file.__ 

Returns the binary content of the file.

| Consumes | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | The path to read.<br><small></small> |
| __fields__<br><small>IdentArray</small> | PathInfo fields that should be returned<br><small></small> |


| Produces | Description |
|:---- |:---- |
| __content__<br><small>ArrayBuffer</small> | Binary content of the file<br><small></small> |
| __---__<br><small>Various</small> | requested PathInfo fields<br><small></small> |


## <small>Action</small> fs-write</code> 
__Write content to a file__ 

| Consumes | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | The path to read<br><small></small> |
| __type__<br><small>MimeTypeString</small> | MIME type of the file<br><small></small> |
| __content__<br><small>ArrayBuffer</small> | Binary content that will be written .<br><small></small> |
| __fields__<br><small>IdentArray</small> | PathInfo fields that should be returned.<br><small></small> |


| Produces | Description |
|:---- |:---- |
| __---__<br><small>Various</small> | requested PathInfo fields<br><small></small> |


## <small>Action</small> fs-delete</code> 
__Delete a file__ 

| Consumes | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | The path to read<br><small></small> |
| __fields__<br><small>IdentArray</small> | PathInfo fields that should be returned.<br><small></small> |


| Produces | Description |
|:---- |:---- |
| __---__<br><small>Various</small> | requested PathInfo fields<br><small></small> |


## <small>Action</small> fs-list</code> 
__List the contents of a directory.__ 

| Consumes | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | The path to list<br><small></small> |
| __type__<br><small>MimeAcceptMask</small> | Filter by MIME type<br><small></small> |
| __glob__<br><small>FileGlobMask</small> | Filter by file name<br><small>E.g. `"*.txt"`</small> |
| __fields__<br><small>IdentArray</small> | PathInfo fields that should be returned<br><small>The same fields should be returned both for the directory itself and listed files.</small> |


| Produces | Description |
|:---- |:---- |
| __list__<br><small>ArrayOfPathInfo</small> | List of files<br><small></small> |
| __---__<br><small>Various</small> | requested PathInfo fields<br><small></small> |


## <small>Action</small> fs-mkdir</code> 
__Create a directory.__ 

| Consumes | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | The path of the directory to be created.<br><small></small> |
| __fields__<br><small>IdentArray</small> | PathInfo Fields that should be returned.<br><small></small> |


| Produces | Description |
|:---- |:---- |
| __---__<br><small>Various</small> | requested PathInfo fields<br><small></small> |


## <small>Action</small> fs-rmdir</code> 
__Delete a directory.__ 

| Consumes | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | The path of the directory to be deleted.<br><small></small> |
| __recursive__<br><small>Boolean</small> | Delete all contained files and subdirectories<br><small>This operation should be atomic, if possible. The API could possibly provide for specifying different failure strategies.</small> |
| __fields__<br><small>IdentArray</small> | PathInfo Fields that should be returned.<br><small></small> |


| Produces | Description |
|:---- |:---- |
| __---__<br><small>Various</small> | requested PathInfo fields<br><small></small> |


## <small>Error</small> not-found</code> 
__The file does not exist.__ 

| Produces | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | <br><small></small> |


## <small>Error</small> not-allowed</code> 
__This action is not allowed.__ 

| Produces | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | <br><small></small> |
| __action__<br><small>IdentString</small> | <br><small></small> |


## <small>Error</small> not-directory</code> 
__This path is not a directory.__ 

| Produces | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | <br><small></small> |


## <small>Error</small> not-empty</code> 
__This path is not a directory.__ 

| Produces | Description |
|:---- |:---- |
| __path__<br><small>PathString</small> | <br><small></small> |

----
Generated from:
````
# "fs"

## Action fs-path
"Return information about a path."

-> PathString path "The path to query."

-> IdentArray fields "Fields that should be returned."

<- FilenameString filename "The cannonical name of the file"
Return `null` if the file does not exist.

<- MimeTypeString type "The MIME type of the file at this path"
Return `"directory"` for directories, `"file"` for files of unknown type and `null` if the file does not exist.

<- IdentArray can "FS actions that can be executed on this file"
e.g.`["read","write"]`

<- Integer size "Byte length of the file"

<- UnixTime ctime "Creation timestamp"

<- UnixTime mtime "Modification timestamp"

## Action fs-info
"Return information about a file."

Behaves the same as `fs-path` except it throws `not-found` if the file does not exist.

-> PathString path "The file to query."

-> IdentArray fields "Fields that should be returned."

<- Various --- "requested PathInfo fields"

## Action fs-read
"Read the content of a file."

Returns the binary content of the file.

-> PathString path "The path to read."

-> IdentArray fields "PathInfo fields that should be returned"

<- ArrayBuffer content "Binary content of the file"

<- Various --- "requested PathInfo fields"

## Action fs-write
"Write content to a file"

-> PathString path "The path to read"

-> MimeTypeString type "MIME type of the file"

-> ArrayBuffer content "Binary content that will be written ."

-> IdentArray fields "PathInfo fields that should be returned."

<- Various --- "requested PathInfo fields"

## Action fs-delete
"Delete a file"

-> PathString path "The path to read"

-> IdentArray fields "PathInfo fields that should be returned."

<- Various --- "requested PathInfo fields"

## Action fs-list
"List the contents of a directory."

-> PathString path "The path to list"

-> MimeAcceptMask type "Filter by MIME type"

-> FileGlobMask glob "Filter by file name"
E.g. `"*.txt"`

-> IdentArray fields "PathInfo fields that should be returned"
The same fields should be returned both for the directory itself and listed files.

<- ArrayOfPathInfo list "List of files"

<- Various --- "requested PathInfo fields"

## Action fs-mkdir
"Create a directory."

-> PathString path "The path of the directory to be created."

-> IdentArray fields "PathInfo Fields that should be returned."

<- Various --- "requested PathInfo fields"

## Action fs-rmdir
"Delete a directory."

-> PathString path "The path of the directory to be deleted."

-> Boolean recursive "Delete all contained files and subdirectories"
This operation should be atomic, if possible. The API could possibly provide for specifying different failure strategies.

-> IdentArray fields "PathInfo Fields that should be returned."

<- Various --- "requested PathInfo fields"

## Error not-found
"The file does not exist."

<- PathString path

## Error not-allowed
"This action is not allowed."

<- PathString path

<- IdentString action

## Error not-directory
"This path is not a directory."

<- PathString path

## Error not-empty
"This path is not a directory."

<- PathString path
````
