

#WebFS API: `fs`

A Web File System is a web app which runs in a window (usually an iframe) and responds to shell's file system requests, which 
it recieves via window messages.

## Table of contents

| Type     | Name     | Description | Consumes | Produces |
| -------- | -------- | ----------- | ---------- | -------- |
|__`action`__ | __[`fs-path`](#action-fs-path)__ | Return information about a path. | `{ path, fields? }` | `{ filename, type, can?, size?, ctime?, mtime? }`|
|__`action`__ | __[`fs-info`](#action-fs-info)__ | Return information about a file. | `{ path, fields? }` | `{ ... }`|
|__`action`__ | __[`fs-read`](#action-fs-read)__ | Read the content of a file. | `{ path, fields? }` | `{ content, ... }`|
|__`action`__ | __[`fs-write`](#action-fs-write)__ | Write content to a file | `{ path, type, content, fields? }` | `{ ... }`|
|__`action`__ | __[`fs-delete`](#action-fs-delete)__ | Delete a file | `{ path, fields? }` | `{ ... }`|
|__`action`__ | __[`fs-list`](#action-fs-list)__ | List the contents of a directory. | `{ path, type?, glob?, fields? }` | `{ list, ... }`|
|__`action`__ | __[`fs-mkdir`](#action-fs-mkdir)__ | Create a directory. | `{ path, fields? }` | `{ ... }`|
|__`action`__ | __[`fs-rmdir`](#action-fs-rmdir)__ | Delete a directory. | `{ path, recursive?, fields? }` | `{ ... }`|
|__`error`__ | __[`fs-not-found`](#error-fs-not-found)__ | The file does not exist. | `{  }` | `{ path }`|
|__`error`__ | __[`fs-not-allowed`](#error-fs-not-allowed)__ | This action is not allowed. | `{  }` | `{ path, action }`|
|__`error`__ | __[`fs-not-directory`](#error-fs-not-directory)__ | This path is not a directory. | `{  }` | `{ path }`|
|__`error`__ | __[`fs-not-empty`](#error-fs-not-empty)__ | This path is not a directory. | `{  }` | `{ path }`|
 
----
## Action `fs-path`
  
Return information about a path.

__request__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` | The path to query. | 
__`fields?`__ | `IdentArray` | Fields that should be returned. | 

__response__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`filename`__ | `FilenameString` | The cannonical name of the file | Return `null` if the file does not exist.
__`type`__ | `MimeTypeString` | The MIME type of the file at this path | Return `"directory"` for directories, `"file"` for files of unknown type and `null` if the file does not exist.
__`can?`__ | `IdentArray` | FS actions that can be executed on this file | e.g.`["read","write"]`
__`size?`__ | `Integer` | Byte length of the file | 
__`ctime?`__ | `UnixTime` | Creation timestamp | 
__`mtime?`__ | `UnixTime` | Modification timestamp | 

----
## Action `fs-info`
  
Return information about a file.

Behaves the same as `fs-path` except it throws `not-found` if the file does not exist.

__request__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` | The file to query. | 
__`fields?`__ | `IdentArray` | Fields that should be returned. | 

__response__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`...`__ | `(various)` | Requested PathInfo fields | 

----
## Action `fs-read`
  
Read the content of a file.

Returns the binary content of the file.

__request__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` | The path to read. | 
__`fields?`__ | `IdentArray` | PathInfo fields that should be returned | 

__response__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`content`__ | `ArrayBuffer` | Binary content of the file | 
__`...`__ | `(various)` | Requested PathInfo fields | 

----
## Action `fs-write`
  
Write content to a file

__request__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` | The path to read | 
__`type`__ | `MimeTypeString` | MIME type of the file | 
__`content`__ | `ArrayBuffer` | Binary content that will be written . | 
__`fields?`__ | `IdentArray` | PathInfo fields that should be returned. | 

__response__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`...`__ | `(various)` | Requested PathInfo fields | 

----
## Action `fs-delete`
  
Delete a file

__request__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` | The path to read | 
__`fields?`__ | `IdentArray` | PathInfo fields that should be returned. | 

__response__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`...`__ | `(various)` | Requested PathInfo fields | 

----
## Action `fs-list`
  
List the contents of a directory.

__request__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` | The path to list | 
__`type?`__ | `MimeAcceptMask` | Filter by MIME type | 
__`glob?`__ | `FileGlobMask` | Filter by file name | E.g. `"*.txt"`
__`fields?`__ | `IdentArray` | PathInfo fields that should be returned | The same fields should be returned both for the directory itself and listed files.

__response__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`list`__ | `Array of PathInfo` | List of files | 
__`...`__ | `(various)` | Requested PathInfo fields | 

----
## Action `fs-mkdir`
  
Create a directory.

__request__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` | The path of the directory to be created. | 
__`fields?`__ | `IdentArray` | PathInfo Fields that should be returned. | 

__response__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`...`__ | `(various)` | Requested PathInfo fields | 

----
## Action `fs-rmdir`
  
Delete a directory.

__request__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` | The path of the directory to be deleted. | 
__`recursive?`__ | `Boolean` | Delete all contained files and subdirectories | This operation should be atomic, if possible. The API could possibly provide for specifying different failure strategies.
__`fields?`__ | `IdentArray` | PathInfo Fields that should be returned. | 

__response__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`...`__ | `(various)` | Requested PathInfo fields | 

----
## Error `fs-not-found`
  
The file does not exist.

__error data__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` |  | 

----
## Error `fs-not-allowed`
  
This action is not allowed.

__error data__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` |  | 
__`action`__ | `IdentString` |  | 

----
## Error `fs-not-directory`
  
This path is not a directory.

__error data__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` |  | 

----
## Error `fs-not-empty`
  
This path is not a directory.

__error data__

Name     | Type     | Description | Details
---------| -------- | ----------- | --------
__`path`__ | `PathString` |  | 
