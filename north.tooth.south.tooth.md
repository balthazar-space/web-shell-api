# NorthTooth/SouthTooth - The communication layer for portlets 

_Portlets_ are components which communicate through HTML5 MessagePorts using  the `NorthTooth/SouthTooth` protocol. Different components` can live inside  the same window, or different windows, iframes, workers, and with some proxying, even on the server. 

Portlet connections are not symmetrical. In each connection, one portlet is the _master_ and the other is the _slave_. Masters tell slaves to execute actions and  optionally ask for a response.  Slaves execute the requested actions, and if a response was requested, return  either a result or an error. Slaves can also trigger events, which masters may catch and handle.

A portlet can have multiple connections to other portlets, in each of which they  can be either the master or the slave. For each connection to a slave, it will have a `DownLink` object, and for each connection to a master, it will have a `UpLink` object. The connected `DownLink` and `Uplink` objects establish two MessageChannels. The first, called the _NorthTooth_ channel, is used for maintaining the connection.  The other, called the _SouthTooth_ channel, is used for sending data, such as  action requests and their results, as well as events, between the two portlets.

Typically, a portlet constructor will return only the portlet´s `UpLink` NorthTooth  port, which can then be used to establish connection to the portlet. 

Initial communication is established through the slave´s UpLink´s NorthTooth  public port, which is given to the DownLink´s `connect` method. This is used throughout the connection for communicating the state of the connection. A SouthTooth link is then opened and used for actual communication between the master and the slave. SouthTooth connections can be broken and reestablished during the lifetime of the connection, e.g. when a web app iframe that contains a portlet component is navigated or reloaded. This is accomplished using NorthTooth proxies.

## <small>Interface</small> DownLink</code> 
 

### <small>Method</small> connect</code> 
 

| Consumes | Description |
|:---- |:---- |
| __north__<br><small>MessagePort</small> | The slave's NorthTooth port<br><small>Provide the `NorthTooth` port of the slave that will respond to messages on this `DownLink`. This is typically returned by the slave´s constructor.</small> |


### <small>Method</small> send</code> 
__Send an action to the slave__ 

| Consumes | Description |
|:---- |:---- |
| __action__<br><small>String</small> | Name of the action<br><small></small> |
| __args__<br><small>Object</small> | Dictionary of action arguments<br><small></small> |
| __transfer__<br><small>Array</small> | Array of transferable objects in the argument dictionary<br><small></small> |


### <small>Method</small> request</code> 
__Send an action to the slave and request a response__ 

| Consumes | Description |
|:---- |:---- |
| __action__<br><small>String</small> | Name of the action<br><small></small> |
| __args__<br><small>Object</small> | Dictionary of action arguments<br><small></small> |
| __transfer__<br><small>Array</small> | Array of transferable objects in the argument dictionary<br><small></small> |
| __timeout__<br><small>Number</small> | How long to wait for an answer<br><small></small> |


| Produces | Description |
|:---- |:---- |
| __result_or_error__<br><small>Promise</small> | A promise that will resolve when the slave answers<br><small>The result returned by the slave will be available in `.then`.<br> Any errors returned by the slave can be caught with `.catch().`</small> |


### <small>Method</small> close</code> 
__Close the connection__ 

### <small>Method</small> on</code> 
__Register an event listener__ 

| Consumes | Description |
|:---- |:---- |
| __event__<br><small>String</small> | Name of the event<br><small></small> |
| __listener__<br><small>Function</small> | The listener to add<br><small></small> |


### <small>Method</small> off</code> 
__Remove an event listener__ 

| Consumes | Description |
|:---- |:---- |
| __event__<br><small>String</small> | Name of the event<br><small></small> |
| __handler__<br><small>Function</small> | The listener to remove<br><small></small> |


## <small>Interface</small> UpLink</code> 
 

### <small>Method</small> connect</code> 
__Connect to master__ 

### <small>Method</small> addAction</code> 
__Add an action handler__ 

When the master sends an action message, the handler will be executed and given  a dictionary of arguments. 

If a response is required, the handler´s return value will be sent to the  master where it will be used to resolve the promise returned by DownLink´s `request` method. Any unhandled exception thrown in the handler will cause an error to be returned to the master and the promise to be rejected.

The handler may also return a promise.

| Consumes | Description |
|:---- |:---- |
| __action__<br><small>String</small> | Action name<br><small></small> |
| __handler__<br><small>Function</small> | The handler to add<br><small></small> |


### <small>Method</small> dispatchEvent</code> 
__Dispatch an event__ 

| Consumes | Description |
|:---- |:---- |
| __event__<br><small>String</small> | Event name<br><small></small> |
| __details__<br><small>String</small> | Event details<br><small></small> |


### <small>Method</small> disconnect</code> 
__Disconnect the SouthTooth connection__ 

### <small>Method</small> close</code> 
__Close the connection__ 

## <small>States</small> DownLink</code> 
__States of the DownLink object__ 

### <small>State</small> open</code> 
__The master is ready to accept connections__ 

The initial state of the master.

Accept on `NorthTooth`:
- __on__ `listening` __reply__ `talking` __then enter state__ `waiting`
- __on__ `closing` __enter state__ `closed`

### <small>State</small> waiting</code> 
__The master is waiting for a data port__ 

Accept on `NorthTooth`:
- __on__ `connecting` __enter state__ `connecting`
- __on__ `closing` __enter state__ `closed`

### <small>State</small> connecting</code> 
__The master is waiting for a data port__ 

Accept on `SouthTooth`:
- __on__ `listening` __reply__ `talking` __then enter state__ `connecting2`

Accept on `NorthTooth`:
- __on__ `closing` __enter state__ `closed`

### <small>State</small> connecting2</code> 
__The master is waiting for the slave to connect__ 

Accept on `SouthTooth`:
- __on__ `connecting` __enter state__ `connected`

Accept on `NorthTooth`:
- __on__ `disconnecting` __enter state__ `waiting`
- __on__ `closing` __enter state__ `closed`

### <small>State</small> connected</code> 
__The data port is open__ 

Accept on `SouthTooth`:
- __on__ `event` __trigger event handlers__
- __on__ `reply` __resolve request promise__

Accept on `NorthTooth`:
- __on__ `disconnecting` __enter state__ `waiting`
- __on__ `connecting` __enter state__ `connecting`
- __on__ `closing` __enter state__ `closed`

### <small>State</small> closed</code> 
__The connection is closed__ 

## <small>States</small> UpLink</code> 
__States of the UpLink object__ 

### <small>State</small> open</code> 
__The slave is listening__ 

The initial state of the slave. 
- Send `listening` on `NorthTooth` , __then:__

- Accept on `NorthTooth`:
      - __on__ `talking` __reply__ `connecting` __then enter state__ `connecting`
  - __on__ `close` __enter state__ `closed`

### <small>State</small> connecting</code> 
__The slave is listening on the data port__ 

- Send `listening` on `SouthTooth` , __then:__
- Accept on `SouthTooth`:
  - __on__ `talking` __reply__ `connecting` __then enter state__ `connected`
- Accept on `NorthTooth`:
  - __on__ `close` __enter state__ `closed`

### <small>State</small> connected</code> 
__The data port is open__ 

- Send `connected` on `SouthTooth` , __then:__

Accept on `SouthTooth`:
- __on__ `action` __trigger action handler__ __then reply__ `result` or `error`

Accept on `NorthTooth`:
- __on__ `close` __enter state__ `closed`

### <small>State</small> closed</code> 
__The connection is closed__ 

## <small>Messages</small> NorthTooth</code> 
 

### <small>Message</small> listening</code> 
__The slave is listening__ 

Sent by the slave to initiate the connection. After the master responds, the slave will send a `connected` message to initiate a `SouthTooth` connection.

| Property | Description |
|:---- |:---- |
| __listening__<br><small>String</small> | Protocol name<br><small>Identifies the protocol. Must be `"NORTH-TOOTH"`.</small> |
| __version__<br><small>Number</small> | Version number<br><small>The master will return the same or smaller version number.</small> |
| __rsvp__<br><small>UUID</small> | Please respond<br><small>Will be returned as `re` by the master.</small> |


### <small>Message</small> talking</code> 
__The master is talking__ 

| Property | Description |
|:---- |:---- |
| __talking__<br><small>String</small> | Protocol name<br><small>Identifies the protocol. Must be `"NORTH-TOOTH"`.</small> |
| __version__<br><small>Number</small> | Version number<br><small>Return the same or smaller version number than the received one.</small> |
| __re__<br><small>UUID</small> | Response token<br><small>Returns the value of `rsvp` from the request.</small> |


### <small>Message</small> connecting</code> 
__The slave is sending a new data port__ 

Once received, the master will invalidate the current `SouthTooth` connection, if it exists, and wait for a new `SouthTooth listening` message on the provided data port.

| Property | Description |
|:---- |:---- |
| __connected__<br><small>String</small> | Protocol name<br><small>Identifies the protocol. Must be `"NORTH-TOOTH"`.</small> |
| __south__<br><small>MessagePort</small> | Data port<br><small>Will be used for `SouthTooth` communication between the master and the slave.</small> |


### <small>Message</small> disconnecting</code> 
__The slave is disconnecting the data port__ 

| Property | Description |
|:---- |:---- |
| __disconnected__<br><small>String</small> | Protocol name<br><small>Identifies the protocol. Must be `"NORTH-TOOTH"`.</small> |


### <small>Message</small> closing</code> 
__The slave is closing the connection port__ 

| Property | Description |
|:---- |:---- |
| __closed__<br><small>String</small> | Protocol name<br><small>Identifies the protocol. Must be `"NORTH-TOOTH"`.</small> |


## <small>Messages</small> SouthTooth</code> 
 

### <small>Message</small> listening</code> 
__The slave is listening__ 

Sent by the slave to initiate the connection. The master will respond with `talking`.

| Property | Description |
|:---- |:---- |
| __listening__<br><small>String</small> | Protocol name<br><small>Identifies the protocol. Must be `"SOUTH-TOOTH"`.</small> |
| __version__<br><small>Number</small> | Version number<br><small>The master will return the same or smaller version number.</small> |
| __rsvp__<br><small>UUID</small> | Please respond<br><small>Will be returned as `re` by the master.</small> |


### <small>Message</small> talking</code> 
__The master is talking__ 

Sent by the master after receiving `listening`.

| Property | Description |
|:---- |:---- |
| __talking__<br><small>String</small> | Protocol name<br><small>Identifies the protocol. Must be `"SOUTH-TOOTH"`.</small> |
| __version__<br><small>Number</small> | Version number<br><small>Return the same or smaller version number than the received one.</small> |
| __session__<br><small>UUID</small> | Session token<br><small>May be used by the slave to reestablish its previous state if it is reloaded or recreated.</small> |
| __rsvp__<br><small>UUID</small> | Please respond<br><small>Will be returned as `re` by the master.</small> |
| __re__<br><small>UUID</small> | Response token<br><small>Returns the value of `rsvp` from the `listening` message.</small> |


### <small>Message</small> connected</code> 
__The slave is ready to accept action requests__ 

Sent by the slave after receiving `talking`.

| Property | Description |
|:---- |:---- |
| __talking__<br><small>String</small> | Protocol name<br><small>Identifies the protocol. Must be `"SOUTH-TOOTH"`.</small> |
| __manifest__<br><small>Object</small> | Slave manifest<br><small>Information provided by the slave about itself. Prescribed by various component APIs.</small> |
| __session__<br><small>String</small> | Session state<br><small>Must be `"new"`, if the slave has no session information stored for the token or `"recovered"`if the session was succesfully recovered.</small> |


### <small>Message</small> action</code> 
__The master is sending an action request__ 

Sent by the master when it wants the slave to execute an action. If `rsvp` is  provided, the result of the action handler or any error will be returned by the  slave either as a `result` message or an `error` message.

| Property | Description |
|:---- |:---- |
| __action__<br><small>String</small> | Name of the action<br><small></small> |
| __args__<br><small>Object</small> | Dictionary of action arguments<br><small></small> |
| __rsvp__<br><small>UUID</small> | Please respond<br><small>Will be returned as `re` by the slave.</small> |


### <small>Message</small> result</code> 
__The slave is sending an action result__ 

| Property | Description |
|:---- |:---- |
| __result__<br><small>Any</small> | The result of an action request<br><small></small> |
| __re__<br><small>UUID</small> | Response token<br><small>Returns the value of `rsvp` from the action request.</small> |


### <small>Message</small> error</code> 
__The slave is sending an action error__ 

| Property | Description |
|:---- |:---- |
| __error__<br><small>String</small> | The error raised on an action request<br><small></small> |
| __details__<br><small>Object</small> | Error details<br><small></small> |
| __re__<br><small>UUID</small> | Response token<br><small>Returns the value of `rsvp` from the action request.</small> |


### <small>Message</small> event</code> 
__The slave is sending an event__ 

| Property | Description |
|:---- |:---- |
| __event__<br><small>String</small> | Event name<br><small></small> |
| __details__<br><small>String</small> | Event details<br><small></small> |

----
<details>
<summary>Generated from:</summary>
````
# "NorthTooth/SouthTooth - The communication layer for portlets"

_Portlets_ are components which communicate through HTML5 MessagePorts using 
the `NorthTooth/SouthTooth` protocol. Different components` can live inside 
the same window, or different windows, iframes, workers, and with some proxying,
even on the server. 


Portlet connections are not symmetrical. In each connection, one portlet is the
_master_ and the other is the _slave_. Masters tell slaves to execute actions and 
optionally ask for a response. 
Slaves execute the requested actions, and if a response was requested, return 
either a result or an error. Slaves can also trigger events, which masters may
catch and handle.

A portlet can have multiple connections to other portlets, in each of which they 
can be either the master or the slave. For each connection to a slave, it will
have a `DownLink` object, and for each connection to a master, it will have a
`UpLink` object.
The connected `DownLink` and `Uplink` objects establish two MessageChannels. The
first, called the _NorthTooth_ channel, is used for maintaining the connection. 
The other, called the _SouthTooth_ channel, is used for sending data, such as 
action requests and their results, as well as events, between the two portlets.

Typically, a portlet constructor will return only the portlet´s `UpLink` NorthTooth 
port, which can then be used to establish connection to the portlet. 

Initial communication is established through the slave´s UpLink´s NorthTooth 
public port, which is given to the DownLink´s `connect` method. This is used throughout
the connection for communicating the state of the connection. A SouthTooth link
is then opened and used for actual communication between the master and the slave.
SouthTooth connections can be broken and reestablished during the lifetime of the
connection, e.g. when a web app iframe that contains a portlet component is
navigated or reloaded. This is accomplished using NorthTooth proxies.

## Interface DownLink

### Method connect
-> MessagePort north "The slave's NorthTooth port" 
Provide the `NorthTooth` port of the slave that will respond to messages on this
`DownLink`. This is typically returned by the slave´s constructor.

### Method send "Send an action to the slave"

-> String action "Name of the action"
-> Object args "Dictionary of action arguments"
-> Array transfer "Array of transferable objects in the argument dictionary"

### Method request "Send an action to the slave and request a response"

-> String action "Name of the action"
-> Object args "Dictionary of action arguments"
-> Array transfer "Array of transferable objects in the argument dictionary"
-> Number timeout "How long to wait for an answer"

<- Promise result_or_error "A promise that will resolve when the slave answers" 

The result returned by the slave will be available in `.then`.<br>
Any errors returned by the slave can be caught with `.catch().`

### Method close "Close the connection"

### Method on "Register an event listener"

-> String event "Name of the event"
-> Function listener "The listener to add"

### Method off "Remove an event listener"

-> String event "Name of the event"
-> Function handler "The listener to remove"

## Interface UpLink

### Method connect "Connect to master"

### Method addAction "Add an action handler"
When the master sends an action message, the handler will be executed and given 
a dictionary of arguments. 

If a response is required, the handler´s return value will be sent to the 
master where it will be used to resolve the promise returned by DownLink´s `request`
method. Any unhandled exception thrown in the handler will cause an error to be
returned to the master and the promise to be rejected.

The handler may also return a promise.

-> String action "Action name"
-> Function handler "The handler to add"

### Method dispatchEvent "Dispatch an event"

-> String event "Event name"
-> String details "Event details"

### Method disconnect "Disconnect the SouthTooth connection"

### Method close "Close the connection"

## States DownLink "States of the DownLink object"

### State open "The master is ready to accept connections"
The initial state of the master.

Accept on `NorthTooth`:
- __on__ `listening` __reply__ `talking` __then enter state__ `waiting`
- __on__ `closing` __enter state__ `closed`
  
### State waiting "The master is waiting for a data port"

Accept on `NorthTooth`:
- __on__ `connecting` __enter state__ `connecting`
- __on__ `closing` __enter state__ `closed`

### State connecting "The master is waiting for a data port"

Accept on `SouthTooth`:
- __on__ `listening` __reply__ `talking` __then enter state__ `connecting2`

Accept on `NorthTooth`:
- __on__ `closing` __enter state__ `closed`


### State connecting2 "The master is waiting for the slave to connect"

Accept on `SouthTooth`:
- __on__ `connecting` __enter state__ `connected`

Accept on `NorthTooth`:
- __on__ `disconnecting` __enter state__ `waiting`
- __on__ `closing` __enter state__ `closed`

  
### State connected "The data port is open"

Accept on `SouthTooth`:
- __on__ `event` __trigger event handlers__
- __on__ `reply` __resolve request promise__

Accept on `NorthTooth`:
- __on__ `disconnecting` __enter state__ `waiting`
- __on__ `connecting` __enter state__ `connecting`
- __on__ `closing` __enter state__ `closed`

### State closed
"The connection is closed"

## States UpLink "States of the UpLink object"

### State open "The slave is listening"
The initial state of the slave. 
- Send `listening` on `NorthTooth` , __then:__

- Accept on `NorthTooth`:
      - __on__ `talking` __reply__ `connecting` __then enter state__ `connecting`
  - __on__ `close` __enter state__ `closed`

### State connecting "The slave is listening on the data port"
- Send `listening` on `SouthTooth` , __then:__
- Accept on `SouthTooth`:
  - __on__ `talking` __reply__ `connecting` __then enter state__ `connected`
- Accept on `NorthTooth`:
  - __on__ `close` __enter state__ `closed`

### State connected "The data port is open"
- Send `connected` on `SouthTooth` , __then:__

Accept on `SouthTooth`:
- __on__ `action` __trigger action handler__ __then reply__ `result` or `error`

Accept on `NorthTooth`:
- __on__ `close` __enter state__ `closed`


### State closed
"The connection is closed"

## Messages NorthTooth

### Message listening
"The slave is listening"
Sent by the slave to initiate the connection. After the master responds, the slave
will send a `connected` message to initiate a `SouthTooth` connection.

$ String listening "Protocol name"
Identifies the protocol. Must be `"NORTH-TOOTH"`.

$ Number version "Version number"
The master will return the same or smaller version number.
 
$ UUID rsvp "Please respond"
Will be returned as `re` by the master.

### Message talking
"The master is talking"

$ String talking "Protocol name"
Identifies the protocol. Must be `"NORTH-TOOTH"`.

$ Number version "Version number"
Return the same or smaller version number than the received one.
 
$ UUID re "Response token"
Returns the value of `rsvp` from the request.

### Message connecting
"The slave is sending a new data port"
Once received, the master will invalidate the current `SouthTooth` connection, if it exists,
and wait for a new `SouthTooth listening` message on the provided data port.

$ String connected "Protocol name"
Identifies the protocol. Must be `"NORTH-TOOTH"`.

$ MessagePort south "Data port"
Will be used for `SouthTooth` communication between the master and the slave.

### Message disconnecting
"The slave is disconnecting the data port"

$ String disconnected "Protocol name"
Identifies the protocol. Must be `"NORTH-TOOTH"`.


### Message closing 
"The slave is closing the connection port"
$ String closed "Protocol name"
Identifies the protocol. Must be `"NORTH-TOOTH"`.

## Messages SouthTooth

### Message listening
"The slave is listening"
Sent by the slave to initiate the connection. The master will respond with
`talking`.

$ String listening "Protocol name"
Identifies the protocol. Must be `"SOUTH-TOOTH"`.

$ Number version "Version number"
The master will return the same or smaller version number.
 
$ UUID rsvp "Please respond"
Will be returned as `re` by the master.

### Message talking
"The master is talking"
Sent by the master after receiving `listening`.
$ String talking "Protocol name"
Identifies the protocol. Must be `"SOUTH-TOOTH"`.

$ Number version "Version number"
Return the same or smaller version number than the received one.

$ UUID session "Session token"
May be used by the slave to reestablish its previous state if it is reloaded
or recreated.

$ UUID rsvp "Please respond"
Will be returned as `re` by the master.
 
$ UUID re "Response token"
Returns the value of `rsvp` from the `listening` message.

### Message connected
"The slave is ready to accept action requests"
Sent by the slave after receiving `talking`.

$ String talking "Protocol name"
Identifies the protocol. Must be `"SOUTH-TOOTH"`.

$ Object manifest "Slave manifest"
Information provided by the slave about itself. Prescribed by various component
APIs.
 
$ String session "Session state"
Must be `"new"`, if the slave has no session information stored for the
token or `"recovered"`if the session was succesfully recovered.


### Message action "The master is sending an action request"
Sent by the master when it wants the slave to execute an action. If `rsvp` is 
provided, the result of the action handler or any error will be returned by the 
slave either as a `result` message or an `error` message.

$ String action "Name of the action"
$ Object args "Dictionary of action arguments"
$ UUID rsvp "Please respond"
Will be returned as `re` by the slave.

### Message result
"The slave is sending an action result"

$ Any result "The result of an action request"
$ UUID re "Response token"
Returns the value of `rsvp` from the action request.

### Message error
"The slave is sending an action error"

$ String error "The error raised on an action request"
$ Object details "Error details"
$ UUID re "Response token"
Returns the value of `rsvp` from the action request.

### Message event
"The slave is sending an event"

$ String event "Event name"
$ String details "Event details"

````
</details>
