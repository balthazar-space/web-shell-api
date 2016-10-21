# WebApp API: Files

The WebApp Files API provides support for File Menu functionality of an application that can view or edit a single document. 


## Manifest
````
  "_south_tooth_files": {
    "extensions": {
      "txt": {
        "label": "Plain Text File",
        "type": "text/plain",
        "accept": "text/*",
        "can": ["open","save","new"]
      }
    },
    "initial_content": <extension>
  }
````

- `extensions` - An object of objects describing files that this application can create/open and/or save, keyed by file extenstion.
  - `.label` - This will be displayed in file dialog boxes etc.
  - `.type` - The mime type of files with this extension.
  - `.accept` (optional) - MIME types of files that can be accepted under this extension, in the same format as for the `accept` attribute on file input elements. Defaults to `.type`.
  - `.can` - An array of action ids indicating which actions the application can perform for this extension.

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

### Action `save`
Save the the current content of the document as a file with this extension.

#### Request
    {
      "action": "save",
      "extension": "txt"
    }

### Response
    {
      "content": ArrayBuffer(<data>)
    }

The returned `content`  must be provided as an ArrayBuffer that will be transfered to the calling window. __Note:__ This means that the ArrayBuffer will no longer will available in the app's window.
