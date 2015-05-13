# Application Insights for Node.js

[![NPM version](https://badge.fury.io/js/applicationinsights.svg)](http://badge.fury.io/js/applicationinsights)
[![Build Status](https://travis-ci.org/Microsoft/ApplicationInsights-node.js.svg?branch=master)](https://travis-ci.org/Microsoft/ApplicationInsights-node.js)

This project provides a Node.js SDK for Application Insights. [Application Insights](http://azure.microsoft.com/en-us/services/application-insights/) is a service that allows developers to keep their applications available, performant, and successful. This node module will allow you to send telemetry of various kinds (event, trace, exception, etc.) to the Application Insights service where they can be visualized in the Azure Portal. 




## Requirements ##
**Install**
```
    npm install applicationinsights
```
**Get an instrumentation key**
>**Note**: an instrumentation key is required before any data can be sent. Please see the "[Getting an Application Insights Instrumentation Key](https://github.com/Microsoft/AppInsights-Home/wiki#getting-an-application-insights-instrumentation-key)" section of the wiki for more information. To try the SDK without an instrumentation key, set the instrumentationKey config value to a non-empty string.




## Usage ##

This will enable request monitoring, unhandled exception tracking, and system performance monitoring (CPU/Memory/RPS)
```javascript
import AppInsights = require("./applicationinsights");
AppInsights.setup("<instrumentation_key>").start();
```

## Customized Usage ##
Disabling auto-collection
```javascript
AppInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    // no telemetry will be sent until .start() is called
    // this prevents any of the auto-collectors from initializing
    .start();
```

Custom monitoring
```javascript
AppInsights.client.trackEvent("custom event", {customProperty: "custom property value"});
AppInsights.client.trackException(new Error("handled exceptions can be logged with this method"));
AppInsights.client.trackMetric("custom metric", 3);
AppInsights.client.trackTrace("trace message");
```

Example with manual request tracking of all "GET" requests
```javascript
var http = require("http");
var appInsights = require('applicationinsights');
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false) // disabling auto-collection for this example
    .start();

// assign common properties to all telemetry
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};

// track a system startup event
appInsights.client.trackEvent("server start");

// create server
var port = process.env.port || 1337
var server = http.createServer(function (req, res) {
    // track all "GET" requests
    if(req.method === "GET") {
        appInsights.client.trackRequest(req, res);
    }

    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello World\n');
}).listen(port);

// track startup time of the server as a custom metric
var start = +new Date;
server.on("listening", () => {
    var end = +new Date;
    var duration = end - start;
    appInsights.client.trackMetric("StartupTime", duration);
});
```



## Contributing ##
**Development environment**

* Install dev dependencies
```
npm install 
```
* (optional) Set an environment variable to your instrumentation key
```
set APPINSIGHTS_INSTRUMENTATIONKEY=<insert_your_instrumentation_key_here>
```
* Run tests
```
npm test
```
