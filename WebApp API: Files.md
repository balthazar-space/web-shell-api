# WebApp API: Files

The WebApp Files API provides support for File Menu functionality. Apps implement common File Menu actions (new,open,save). Apps are only given 

## Rationale
- __Common use case:__ Many apps are viewers or editors for various file types.
- __Security:__ Editors and viewers do not need and should not be given general access to user files or
other information about the user, especially if they're hosted on third-party servers.
- __Simplifying app development:__ Providing for file storage is often the hardest part of developing a web app.
Integrating authorization and file storage APIs and providing the required complex user interface are beyond the scope of
e.g. a simple painting web app and often beyond the means of its developer. 

__Note:__ The current specification assumes that the app that implements this API can view or edit a 
single file at a time, so that the shell can know which file is opened in the app's iframe.
Other functionalities may require extensions to the specification.

## Manifest
````json
  "_south_tooth_files": {
    "extensions": {
      "txt": {
        "label": "Plain Text File",
        "type": "text/plain",
        "accept": "text/*",
        "actions": ["open","save","new"]
      }
    },
    "default_extension": "txt",
    "contains_extension": "txt"
  }
````

- `extensions` - An object of objects describing files that this application can create/open and/or save, keyed by file extenstion.
  - `label` - This will be displayed in file dialog boxes etc.
  - `type` - The mime type of files with this extension.
  - `accept` (optional) - MIME types of files that can be accepted under this extension, in the same format 
  as for the `accept` attribute on file input elements. Defaults to `type`.
  - `actions` - An array of action ids indicating which actions the application can perform for this extension.
- `deafult_extension` - The default extension for actions. Note: This is only a hint for the shell. 
The actual extension will be provided in action calls.
  
## Actions

### Action `file-new`

Create a new (usually empty) document that can be saved as a file with this `extension`.

#### Request
    {
      "action": "file-new",
      "extension": "txt"
    }

#### Response
	"OK"

### Action `file-open`

Open a file with this `extension` and `content`.

#### Request
    {
      "action": "file-open",
      "extension": "txt",
      "type": "text/plain",
      "content": ArrayBuffer(<binary data>)
    }

- `type` - is the actual mime type of the file, as reported by the source file system.
- `content` - is an ArrayBuffer transfered from the calling window.
    
#### Response
	"OK"

### Action `file-save`
Save the the current content of the document as a file with this extension.

#### Request
    {
      "action": "file-save",
      "extension": "txt"
    }

### Response
    {
      "content": ArrayBuffer(<data>)
    }

The returned `content`  must be provided as an ArrayBuffer that will be transfered to the calling window. __Note:__ This means that the ArrayBuffer will no longer will available in the app's window.

