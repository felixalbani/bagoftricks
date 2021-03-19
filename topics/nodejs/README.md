
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

## Lifecycle of Node process

### Termination 
There are several things that can cause a Node.js process to terminate. Some of them are preventable, like when an error is thrown, while others cannot be prevented, like running out of memory. The process global is an Event Emitter instance and, when a graceful exit is performed, will emit an exit event. Application code can then listen to this event to perform some last minute synchronous cleanup work.

### process.exit()
Useful when building scripts when you know that your process has reached the end of its lifetime. The code value is optional and defaults to `0`, which represents a successful process run. Any non-zero number means a failure happened. These values are used by many different external tools. For example, when a test suite runs, a non-zero value means the tests have failed.

```javascript
function checkConfig(config) {
  if (!config.host) {
    console.error("Configuration is missing 'host' parameter!");
    process.exit(1);
  }
}
```

Don't ever use `process.exit()` on library, instead throw an exception.

### Runtime errors

- `throw new Error(msg)`: When an uncaught error is thrown the stack trace is printed in the console and the process terminates with an exit status of 1. Here's an example of such an exception:

```javascript
/tmp/foo.js:1
throw new TypeError('invalid foo');
^
Error: invalid foo
    at Object.<anonymous> (/tmp/foo.js:2:11)
    ... TRUNCATED ...
    at internal/main/run_main_module.js:17:47
```

- `process.on('uncaughtException',...`: The process global is an Event Emitter and can be used to intercept such uncaught errors by listening for the uncaughtException event. Here's an example of how to use it, intercepting an error to send an asynchronous message before exiting:

```javascript
const logger = require('./lib/logger.js');
process.on('uncaughtException', (error) => {
  logger.send("An uncaught exception has occured", error, () => {
    console.error(error);
    process.exit(1);
  });
});
```

- **Unhandled rejections**: Promise Rejections are pretty similar to thrown errors. A promise can reject either if the reject() method in a promise is called, or an error is thrown inside of an asynchronous function. The following two examples are mostly equivalent in this regard:

```javascript
Promise.reject(new Error('oh no'));

(async () => {
  throw new Error('oh no');
})();
```

You can also intercept events when these unhandled rejections happen, listening for another event on the process object:

```javascript
process.on('unhandledRejection', (reason, promise) => {});
```

- When an Event Emitter emits an error event that doesn't have a listener on it, the Event Emitter will throw the argument that was emitted. This will then spit out an error and cause the process to exit. Here's an example of what is printed in the console:

```javascript
events.js:306
    throw err; // Unhandled 'error' event
    ^
Error [ERR_UNHANDLED_ERROR]: Unhandled error. (undefined)
    at EventEmitter.emit (events.js:304:17)
    at Object.<anonymous> (/tmp/foo.js:1:40)
    ... TRUNCATED ...
    at internal/main/run_main_module.js:17:47 {
  code: 'ERR_UNHANDLED_ERROR',
  context: undefined
}
```

Be sure to listen for error events in the Event Emitter instances you work with so that your application may gracefully handle the event without crashing.

### Signals
Signals are an operating system-provided mechanism for sending small numeric messages from one program to another. These numbers are often referred by a constant string equivalent. For example, the signal SIGKILL represents a numeric signal of 9. Signals can have different purposes but are often used to terminate a program in some capacity.

Different operating systems can have different signals defined, but the following list is mostly universal:



| Name    | Number | Handleable | Node.js Default | Signal Purpose                              |
|---------|--------|------------|-----------------|---------------------------------------------|
| SIGHUP  | 1      | Yes        | Terminate       | Parent terminal has been closed             |
| SIGINT  | 2      | Yes        | Terminate       | Terminal trying to interrupt, à la Ctrl + C |
| SIGQUIT | 3      | Yes        | Terminate       | Terminal trying to quit, à la Ctrl + D      |
| SIGKILL | 9      | No         | Terminate       | Process is being forcefully killed          |
| SIGUSR1 | 10     | Yes        | Start Debugger  | User-defined signal 1                       |
| SIGUSR2 | 12     | Yes        | Terminate       | User-defined signal 2                       |
| SIGTERM | 12     | Yes        | Terminate       | Represents a graceful termination           |
| SIGSTOP | 19     | No         | Terminate       | Process is being forcefully stopped         |

Handling these signals in your Node.js programs can be done by listening for more events on the process object:

```javascript
console.log(`Process ID: ${process.pid}`);
process.on('SIGHUP', () => console.log('Received: SIGHUP'));
process.on('SIGINT', () => console.log('Received: SIGINT'));
setTimeout(() => {}, 5 * 60 * 1000); // keep process alive
```

Run this program in a terminal window, then press Ctrl + C, and the process won't die. Instead, it'll state that it has received the SIGINT signal. Switch to another terminal window and execute the following command based on the printed Process ID value:

```bash
$ kill -s SIGHUP <PROCESS_ID>
```
This shows how one program is able to send a signal to another program and your Node.js program running in the first terminal will print that it received the SIGHUP signal.



## References

* [1] Logging in Node.js - Online [February 18, 2021] Available: https://blog.logrocket.com/node-js-logging-best-practices/
* [2] - javscript.info - Online [] Available: https://javascript.info/js
* [3] - The death of a node process - Online [March 8, 2021] Available: https://thomashunter.name/posts/2021-03-08-the-death-of-a-nodejs-process?s=09

