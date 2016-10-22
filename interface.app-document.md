#WebApp API: `app-document`

## Table of contents
+ [Action `app-document-new`](#action-app-document-new)
+ [Action `app-document-open`](#action-app-document-open)
+ [Action `app-document-save`](#action-app-document-save)

----
## Action `app-document-new`
  
Save a file from an app window.

__request__

Name     | Type     | Description
---------| -------- | -----------
`extension` | `ExtensionString` | The extension of the document to create.

----
## Action `app-document-open`
  
Open a file in an app window.

__request__

Name     | Type     | Description
---------| -------- | -----------
`extension` | `ExtensionString` | The extension of the document that will be opened.
`type`? | `MimeTypeString` | The MIME type of the document that will be opened. Defaults to the type specified in the app manifest for the extension.
`content` | `ArrayBuffer` | The binary content of the document.

----
## Action `app-document-save`
  
Save a file from an app window.

__request__

Name     | Type     | Description
---------| -------- | -----------
`extension` | `ExtensionString` | The extension of the document to save.

__response__

Name     | Type     | Description
---------| -------- | -----------
`content` | `ArrayBuffer` | The binary content of the document.
