napp.alloy.adapter.restapi
==========================

RestAPI Sync Adapter for Titanium Alloy Framework.


## How To Use

Simple add the following to your model in `PROJECT_FOLDER/app/models/`.

	exports.definition = {	
		config: {
			"URL": "http://example.com/api/modelname",
			//"debug": 1, 
			"adapter": {
				"type": "restapi",
				"collection_name": "MyCollection",
				"idAttribute": "id"
			},
			"headers": { // your custom headers
	            "Accept": "application/vnd.stackmob+json; version=0",
		        "X-StackMob-API-Key": "your-stackmob-key"
	        },
	        "parentNode": "news.domestic" //your root node
		},		
		extendModel: function(Model) {		
			_.extend(Model.prototype, {});
			return Model;
		},	
		extendCollection: function(Collection) {		
			_.extend(Collection.prototype, {});
			return Collection;
		}		
	}

Then add the `restapi.js` to `PROJECT_FOLDER/app/assets/alloy/sync/`. Create the folders if they dont exist. 

Use the `debug` property in the above example to get logs printed with server response to debug your usage of the restapi adapter.

### Lets see this in action

In your Alloy controller, do would use the REST API adapter like this:

```javascript
var collection = Alloy.createCollection("MyCollection"); //or model
//the fetch method is an async call to the remote REST API. 
collection.fetch({ 
	success : function(){
		_.each(collection.models, function(element, index, list){
			// We are looping through the returned models from the remote REST API
			// Implement your custom logic here
		});
	},
	error : function(){
		Ti.API.error("hmm - this is not good!");
	}
});
```


## Special Properties


### Custom Headers

Define your own custom headers. E.g. to add a BaaS API

	"headers": {
		"Accept": "application/vnd.stackmob+json; version=0",
		"X-StackMob-API-Key": "your-stackmob-key"
	}

Define your own dynamic headers. E.g. adding a dynamic cookie value

	"headers" : function(){
		return {
			"Cookie" : "access=" + someCustomHashFunction();
		};
	}

Custom headers can be added to either the collection or the model or both.

### Nested Result Objects

Lets say you have a REST API where the result objects are nested. Like the Twitter search API. It has the found tweets in a results object. 
Use the `parentNode` to specify from which root object you want to parse children objects. 


	config: {
		...
		"parentNode" : "results"
	}
	
It has support for nested objects. 
	
	config: {
		...
		"parentNode" : "news.domestic"
	}

Since v1.1.1 - you can specify this object as a function instead to custom parse the feed. Here is an example: 

*Feed:* 
http://www.google.com/calendar/feeds/developer-calendar@google.com/public/full?alt=json&orderby=starttime&max-results=15&singleevents=true&sortorder=ascending&futureevents=true

*Custom parsing:*

```javascript
parentNode: function (data) {
	var entries = [];

	_.each(data.feed.entry, function(_entry) {
		var entry = {};

		entry.id = _entry.id.$t;
		entry.startTime = _entry.gd$when[0].startTime;
		entry.endTime = _entry.gd$when[0].endTime;
		entry.title = _entry.title.$t;
		entry.content = _entry.content.$t;

		entries.push(entry);
	});

	return entries;
}
```



## Changelog

**v1.1.5**  
Added functionality for dynamic headers

**v1.1.4**  
Added search mode

**v1.1.3**  
Added support for accessing the error object. Issue #29 Thanks @alexandremblah

**v1.1.2**  
JSON.parse errors are now caught. Thanks @FokkeZB

**v1.1.1**  
Added support parentNode as a function for custom parsing. thanks @FokkeZB

**v1.1.0**  
Added support for Nested Result Objects  
Added support for Custom Headers  
Code cleanup  

**v1.0.6**  
Added support for idAttribute

**v1.0.5**  
Added HTTP Response code and error message

**v1.0.4**  
Added debug

**v1.0.3**  
Alloy 1.0.0.  
Fix bug in rest url being global 

**v1.0.2**  
Added urlparams

**v1.0.1**  
Android bugfixes

**v1.0**  
init

## Author

**Mads Møller**  
web: http://www.napp.dk  
email: mm@napp.dk  
twitter: @nappdev  

## License

    Copyright (c) 2010-2013 Mads Møller

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in
    all copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
    THE SOFTWARE.