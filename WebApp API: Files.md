# WebApp API: Files

The WebApp Files API provides support for File Menu functionality, useful for apps that are viewers or editors for various file types.

This API does not give web apps the power to read or write any files on their own initiative. Instead, when the user
wants to open a file in an app (e.g. having selected it in the File Open dialog provided by the shell), the shell will 
read the file contents from a Web File System and instruct the app to open the contents. Conversely, when the user wants
to save the file, the shell will instruct the app to provide the updated contents, which the shell will then write
to a Web File System.

---

## Table of Contents
+ [Synopsis](#Synopsis)
+ [Rationale](#rationale)
+ [Manifest](#manifest)
+ [Action `app-file-new`](#action-app-file-new)
+ [Action `app-file-open`](#action-app-file-open)
+ [Action `app-file-save`](#action-app-file-save)
+ [Event `app-file-changed`](#event-app-file-save)
+ [Notes](#notes)

---

## Synopsis

    @action app-file-new (extension,token) => "OK"
    Create new document.

    @action app-file-open (extension,type,content,token) => "OK"
    Open document with content.

    @action app-file-save (extension,token) => {content}
    Save document content.
    
    @event app-file-changed (token)
    The document has changed since last save.

    @error rejected(reason)
    The action was rejected.
    
    @error failed(reason)
    An unhandled exception occurred.

---

## Rationale
- __Common use case:__ Many apps are viewers or editors for various file types.
- __Security:__ Editors and viewers do not need and should not be given general access to user files or
other information about the user, especially if they're hosted on third-party servers.
- __Simplifying app development:__ Providing for file storage is often the hardest part of developing a web app.
Integrating authorization and file storage APIs and providing the required complex user interface are beyond the scope of
e.g. a simple painting web app and often beyond the means of its developer. 

---

## Manifest extension
Apps that implement this API should add the following to their manifest:

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
```

- `extensions` - An object of objects describing files that this application can create/open and/or save, keyed by file extenstion.
  - `label` - This will be displayed in file dialog boxes etc.
  - `type` - The mime type of files with this extension.
  - `accept` (optional) - MIME types of files that can be accepted under this extension, in the same format 
  as for the `accept` attribute on file input elements. Defaults to `type`.
  - `actions` - An array of action ids indicating which actions the application can perform for this extension.
- `deafult_extension` - The default extension for actions. Note: This is only a hint for the shell. 
The actual extension will be provided in action calls.

---

## Action `app-file-new`

Create a new (usually empty) document that can be saved as a file with this `extension`.

### Request

    {
      "action": "app-file-new"
    }

### Response

    "OK"

---

## Action `app-file-open`

Open a file with this `extension` and `content`.

### Request

   {
      "action": "app-file-open",
      "extension": "txt",
      "type": "text/plain",
      "content": ArrayBuffer(<binary data>)
    }

- `type` - is the actual mime type of the file, as reported by the source file system.
- `content` - is an ArrayBuffer transfered from the calling window.
    
### Response
	"OK"

---

## Action `app-file-save`
Save the the current content of the document as a file with this extension.

### Request

    {
      "action": "app-file-save",
      "extension": "txt"
    }

### Response

    {
      "content": ArrayBuffer(<binary data>)
    }

The returned `content`  must be provided as an ArrayBuffer that will be transfered to the calling window. __Note:__ This means that the ArrayBuffer will no longer will available in the app's window.

---

## Event `app-file-changed`

The app should emit this when the document is first changed since the last succesful `save` action.

---

## Notes

### Reloading
When an editor app is reloaded, it should recover its document, to prevent loss of user changes. Strategies for this include:

1. An app can save document changes in its browser storage or its server and use session tokens which are passed with
every action request to distinguish between autosaved documents on reload.
2. A shell implementation could ask the app for document save on every change, and store it in a temporary buffer, and supply
it back to the app on reload.

The current specification assumes that every app does (1). Approach (2) can be forced by the shell on any compliant app, but
it could overwhelm the app if producing the file takes a non-trivial time, and should therefore be negotiated.

### Single Document Interface
The current specification assumes that the app that implements this API can view or edit a 
single file at a time, so that the shell can know which file is opened in the app's iframe.
Other functionalities may require extensions to the specification.




