# web-shell-api
API for communication between Web Applications and Web File Providers through a Web Shell

## Diagram
![Control Diagram](https://raw.githubusercontent.com/zocky/web-shell-api/master/diagram.png)

## Data Flow For "File Open"

```` js
UI >> {
  "window": "<window id>"
  "action": "file-open",
  "path": "user@fileprovider/path/to/file.txt",
} >> shell >> {
  "action": "fs-read",
  "path": "user@fileprovider/path/to/file.txt"
} >> fs >> {
  "action": "fs-read",
  "path": "path/to/file"
} >> webfs["user@fileprovider"] /* webfs iframe gets the file from file provider web service*/ >> { 
  "result": {
    "stat": {
      type: "text/plain",
      size, ctime, mtime, etc
    },
    "can": ['read','write'],
    "content": [binaryData],
  }
} >> fs >> shell >> {
  "window" : "<window id">,
  "action" : "file-open",
  "extension": "txt"
  "type": "text/plain",
  "content": [binaryData],
} >> session >> {
  "action" : "file-open",
  "extension": "txt"
  "type": "text/plain",
  "content": [binaryData],
} >> appsession["<window-id>"] >> {
  "file-session-token": "<uuid>",
  "action" : "file-open",
  "extension": "txt"
  "type": "text/plain",
  "content": [binaryData],
} >> app /* app iframe opens opens the file*/ >> {
  "result" : "OK"
} -> appsession -> shellsession -> shell -> ui 
````
