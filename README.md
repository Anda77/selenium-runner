selenium-runner
===============

Run a [{url+JSTest}, ..] combo in selenium grid, in parallel, in multiple browsers.

You need a working [selenium-grid-nodes](http://code.google.com/p/selenium/wiki/Grid2) stack.
Or, you could use [saucelabs](https://saucelabs.com/).

Basically, this module will launch a set of tests, in the browsers you want.
A test is defined by a URL to visit and a JavaScript callback to execute when the page
is ready.

This JavaScript callback is given a `browser` object which exposes the full selenium's [JsonWireProtocol](http://code.google.com/p/selenium/wiki/JsonWireProtocol) in JavaScript.

Have a look at [admc/wd](https://github.com/admc/wd/) for all available methods.

```bash
npm install
node example
```

example.js
```js
// What we want: test if http://google.com title is 'Google' is the main browsers.

var seleniumRunner = require('./');

// Actual initialization takes place in

// tests to run
var tests = [{
  url: 'http://www.google.com',
  exec: checkTitle
}];

// where is the selenium-grid ?
var remoteCfg = {
  "host": "127.0.0.1",
  "port": 4444
};

// how many tests a browser can do in parallel ?
var concurrency = 2;

// which browser to launch ?
// Please, always put browser versions in strings. Selenium requirement.
var browsers = [{
  "browserName": "internet explorer",
  "version": "9"
}, {
  "browserName": "chrome",
  "version": "latest"
}];

// API methods available in browser: see https://github.com/admc/wd/
function checkTitle(browser, cb) {
  browser.title(function(err, title) {
    if (err !== null) cb(err);

    if (title === 'Google') {
      cb(null);
    } else {
      cb(new Error('UH OH TEST FAILED!'))
    }
  });
}

function testCallback(err, context) {
  console.log(arguments);
  // For each browser, you get the result of calling checkTitle here
  // You always get the context: browser + called url
}

function allTestsEnded(err) {
  console.log(arguments);
  // When all tests have finished running, you get this callback
}

// Let's go baby
seleniumRunner({
  remoteCfg: remoteCfg,
  concurrency: concurrency,
  browsers: browsers
}, tests, testCallback, allTestsEnded);
```