
## History of ECMAScript (ES)
The term “ECMAScript” is used to denote language versions (such as ECMAScript 5). In practice, the terms “ECMAScript” and “JavaScript” are interchangeable. If JavaScript means “ECMAScript as implemented by Mozilla and others” then the the former is a dialect of the latter. 

### ECMAScript Editions

* The Original JavaScript ES1 ES2 ES3 (1997-1999)
* The First Main Revision ES5 (2009)
    * Added `strict mode`
    * Added JSON support
    * Added `String.trim()`
    * Added `Array.isArray()`
    * Added `Array` iteration methods
* The Second Revision ES6 (2015)
    * Added `let` and `const`
    * Added `default` parameter values
    * Added `Array.find()`
    * Added `Array.findIndex()`
* ES6 yearly additions:
    * 2016
    * 2017
    * 2018

> **_NOTE:_**  ECMAScript 1 - 6 is fully supported in all modern browsers.

Additionally, you can use polyfills and [Babel](https://babeljs.io/) for transpiling (source-to-source compilers) of newer features to older runtimes.

## Logging

Some of the most popular logging libraries for Node are [Winston](https://www.npmjs.com/package/winston), [Bunyan](https://www.npmjs.com/package/bunyan), and [Log4js](https://www.npmjs.com/package/log4js).

### Log HTTP requests in Node with Morgan

Another best practice is to log your HTTP request in your Node.js application. One of the most used tools to accomplish this is [Morgan](https://www.npmjs.com/package/morgan), which gets the server logs and systematizes them to make them more readable.

### Configure Winston with Morgan
If you choose to use the Winston library you can easily integrate and configure with Morgan. For more read [here](https://blog.logrocket.com/node-js-logging-best-practices/)

### Define log levels
There are several log levels and it is important to know them and their uses. Each log level gives a rough direction to how important and urgent the message is. Below are the common levels:

* Error: important events that will be cause the program execution to fail
* Warn: crucial events that should be noticed to prevent fails
* Info: important events that details a completed task
* Debug: mostly used by developers

### Use logs with a log management system
Log managements systems allow you to track and analyze logs as they happen real-time, which in turn can help improve your code. As you can see in the example below, a log management system can help you keep track of useful data including backend errors, anomalies, log sources, and production errors.

Alternatives: Sentry, Loggly, McAfee Enterprise, Graylog, Splunk, Logmatic, or Logstash.

### Health monitoring tools
Health monitoring tools are a good way to keep track of your server performance and identify causes of application crashes or downtime. Most health monitoring tools offer error tracking as well as alerts and general performance monitoring. Some developers find error tracking particularly frustrating in Node.js, so using a health monitoring tool can help alleviate some of those difficulties.

Below are few popular monitoring tools for Node.js:

* PM2
* Sematext
* Appmetrics
* ClinicJS
* AppSignal
* Express Status Monitor

## References

* [1] Logging in Node.js - Online [February 18, 2021] Available: https://blog.logrocket.com/node-js-logging-best-practices/
* [2] - javscript.info - Online [] Available: https://javascript.info/js

