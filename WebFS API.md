

# WebFS API: File Operations

Provides a common API for file operations for Web File Systems. Web FS Connectors provide wrappers for
common file system operatons for various file storage web services.

## Table of Contents
+ [Manifest](#manifest)
+ [Actions](#actions)
  + [Action `fs-path`](#action-fs-path)
  + [Action `fs-info`](#action-fs-info)
  + [Action `fs-list`](#action-fs-list)
  + [Action `fs-read`](#action-fs-read)
  + [Action `fs-write`](#action-fs-write)


## Manifest

```json
  "_north_tooth_fs": {
     "can": ["list","path","info","read","write","mkdir","rmdir"]
  }
```

## Actions

### Action `fs-path`

Returns information about a path.

#### Request

```json
{
    "action": "fs-path",
    "path": "path/to/file.txt",
    "fields": ["type","size","can"]
}
```
- `fields` - Optional. An array of names of fields that should be returned. If omitted, all fields should be returned.

#### Response

This `path` is a text file:

```json
{
    "type": "text/plain",
    "can": [ "read", "write" ],
    "size": 1234,
    "ctime": 1234567890,
    "mtime": 1234567890,
    "atime": 1234567890,
    "cuser": "CreatedByUser",
    "muser": "EditedByUser",
    "auser": "ViewedByUser",
    "owner": "OwnedByUser"
}
```    
This `path` is a directory:  
```json
    {
        "type": "directory",
        "can": [ "list","rmdir"],
        "owner": "OwnedByUser"
    }
```

This `path` does not exist, but can be written to:  

    {
        "can": ["write","mkdir"]
    }
    
This `path` does not exist, and cannot be written to:  

    {
        "can": []
    }

- `can` - An array of fs-action ids that can be performed at this path.
- `type` - The MIME type of the file.
  - If the path is a directory, return `"directory"`.
  - If the path is a file of an unknown type, return `"file"`.
  - If the path does not exist, omit `type`.

If the path does not exist, only `can` should be returned. If the file system cannot provide information for any of the
requested fields, it should omit it from the response, except for `type` as described bellow:

### Action `fs-info`

Behaves the same as `fs-stat`, except it throws an error if the file does not exist.

#### Request

    {
        "action": "fs-path",
        "path": "path/to/file.txt",
        "fields": ['type','size','can']
    }
    
#### Response
See action `fs-path`;

#### Errors
- `not found`

### Action `fs-list`

Read the directory at this `path` and return the list of its files.

#### Request

    {
      "action": "fs-list",
      "path": "path/to/directory",
      "fields": [...],
    }

#### Response

Returns the same information for the directory as `fs-info`, and adds:

    {
      "files": [<fs-info>],
    }

- `files` - An array of objects. Each object has the same format as the response of `fs-info`, and additionally include 
a string property `filename`.

#### Errors
- `not found`
- `not allowed`
- `not a directory`

### Action `fs-read`

Read the file at this `path` and return its contents.

#### Request
    {
      "action": "fs-read",
      "path": "path",
      "fields": [...]
    }

#### Response
Returns the same information as `fs-info`, and adds:

    {
      "content": ArrayBuffer(<binary data>),
    }

#### Errors
- `not found`
- `not allowed˙

### Action `fs-write`

Write the this binary `content` at this `path` and return the file's new info.

#### Request
    {
      "action": "fs-write",
      "path": "path",
      "content": ArrayBuffer(<binary data>),
      "fields": [...]
    }

#### Response
Returns the same information as `fs-info`.

#### Errors
- `not found` - Return if the path is neither writable nor could be writable.
- `not allowed˙ - Return if the path could be writable, but currently (e.g. for the current user) isn't.
