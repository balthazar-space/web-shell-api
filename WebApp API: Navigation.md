# WebApp API: Navigation

The WebApp Navigation API provides support for Navigation/History functionality of an app. Navigation actions don't take any arguments and don't return any values, but if they're succesfully carried out, the app should trigger a `navigation-navigate`. A `navigation-navigate` event should also be triggered immediately after the app loads and connects to the shell.

## Rationale

- __Reloading apps:__ When apps are reloaded, they should ideally restore the previous state. Many applications use client or server routing for maintaining their state/workflow. Changes in the URL therefore need to be reported to the shell.
- __History in iframes:__ Apps often depend on the browser's history functionality. However, this works only in the top window, and apps that run in iframes must actively cooperate for history functionality to work correctly per iframe.
- __Unified UI:__ Apps that don't track their state by url can use the common UI interface for equivalent actions.

## Manifest
````json 
{
	"_south_tooth_navigation": {
    	"actions": ["back","forward","home","up"]
  	}
}
````
  - `can` - An array of action ids indicating which actions the application can perform for this extension.

## Actions

### Action `navigation-forward`

#### Request
````json
{
  "action": "navigation-forward"
}
````

#### Response
````json
"OK"
````

### Action `navigation-back`
   
#### Request
````json
{
  "action": "navigation-back"
}
````

#### Response
````json
"OK"
````


### Action `navigation-home`

#### Request
````json
{
  "action": "navigation-home"
}
````

#### Response
````json
"OK"
````

### Action `navigation-up`

#### Request
````json
{
  "action": "navigation-up"
}
````

#### Response
````json
"OK"
````


## Events
### Event `navigation-navigate`
#### Event object
````json
{
	"event": "navigation-navigate",
	"url": "<new url>",
	"title": "<new title>",
	"can": ["home","back","forward","up"]
}
````
    
- `url` - The new URL of the app.
- `title` - The new title of the app.
- `can` - An array of action ids that are meaningful from this url.
