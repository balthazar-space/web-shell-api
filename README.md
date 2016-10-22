__Disclaimer:__ This is an API specification under development. It has absolutely no official status, 
and is not supported nor standardized by anybody. 
It has as yet no actual implementation (though a proof of concept demo can be seen at https://south-tooth.hyperdev.space).
Treat it as a curiosity or food-for-thought.

# Web Shell API Specification

A __web shell__ per this specification is a web page which runs in a browser window (_the shell window_) and
manages and controls _web apps_ (WebApp API-compliant webpages that open in iframes) and _web file systems_
(WebFS API-compliant webpages that also open in iframes) and responds to action requests from 
ShellControl API-compliant _shell controllers_.

A web shell communicates with its dependant apps and file systems through DOM window messages. Apps and file
systems respond to _action requests_ sent by the shell. They can also generate _events_ that the shell can
handle or pass on to its controller(s).

A _shell controller_ is a piece of software that controls the shell. This can be a GUI or a programatic
interface. The shell controller communicates with the shell through the ShellControl API. The shell only responds
to controllers that share its origin.

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
