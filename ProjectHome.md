JSLitmus is a lightweight tool for creating ad-hoc JavaScript benchmark tests.  Features include ...
  * Single-file install ([JSLitmus.js](http://broofa.com/Tools/JSLitmus/JSLitmus.js))
  * Works on Firefox, Opera, Safari, IE, Google Chrome, and iPhone browsers
  * Adaptive test cycles - tests take ~1-2 seconds, regardless of the operation
  * Google Chart + TinyUrl integration
  * Open Source MIT-style license (see source)



## Getting Started ##

JSLitmus tests are easy to create ...
  1. Create an html file that includes [JSLitmus.js](http://broofa.com/Tools/JSLitmus/JSLitmus.js)
> 2. Call `JSLitmus.test(test_title, test_function)` to provide one or more test functions
> 3. Open the html file in a browser and click "Run Tests"

A test can be as simple as [simple\_test.html](http://broofa.com/Tools/JSLitmus/simple_test.html):
```
<script src="JSLitmus.js"></script>

<script>
JSLitmus.test('Empty function test', function() {});
</script>
```
This is an empty test and the results will show "Infinity" operations per second, which you may or may not have expected.  If you were hoping to see how rapidly an empty function could be called, simply uncheck "Normalize results".  (You'll probably also want to read the section on "Normalized Results", below.)

But that's the barebones version things.  To get an idea of what a full test file/suite looks like, take a look at [demo\_test.html](http://broofa.com/Tools/JSLitmus/demo_test.html) (be sure to view the source to see the fully commented markup.)  It shows how to ...

  * Set up multiple tests in one file
  * Implement tests as either non-looping or looping functions
  * Add a test title to your chart
  * Create a test file with valid XHTML markup

## As An Embeddable Widget ##
JSLitmus can also be embedded in existing web pages/apps.  Simply provide an element with `id="jslitmus_container"`, and JSLitmus will place the test UI inside that element. (Make sure the element is defined prior to the onload event firing, however).  You can view the source of this page for an example.

## Testing Fast Operations ##

JSLitmus determines the performance of your code by running it repeatedly until a sufficient amount of time has passed to obtain a valid result.  This requires an iteration loop, of course.  But for fast operations - operations that can be run more than ~100K/second or more - the overhead of the iteration loop can overshadow the performance of your test code.  JSLitmus has two features that address this issue.

### Normalized Results ###
JSLitmus has a "**Normalize results**" checkbox that is enabled by default.  With this enabled, the iteration loop overhead is subtracted when tabulating the test results.

Normalization may occasionally cause a test to display "∞" or "Infinity" operations per second.  This occurs if a test has performance comparable to the empty tests used to calibrate the iteration loop overhead.  This may also happen if your CPU was busy with another process when JSLitmus ran it's calibration tests. (The tests take place immediately before the first of your tests is run.)

### Looping Functions ###

The simplest form of a test function is a <strong>non-looping function</strong> like this:
```
JSLitmus.test('a non-looping test', function() {
  // Your test code goes here
});
```

With this type of test JSLitmus supplies an iteration loop for you and invokes your function from inside that loop.  But this adds the overhead of an extra function call on each iteration.  For fast operations this makes it difficult to determine how long an operation actually takes, even with "Normalized results" turned on.  To mitigate this problem, implement your test as a **looping test function** like this:

```
JSLitmus.test('a non-looping test', function(count) {
  while (count--) {
        // Your test code goes here
  }
});
```

JSLitmus will automatically recognize this form of test function (because it declares a "count" argument) and pass in the iteration count necessary for testing.  This eliminates the per-iteration function call overhead, and produces more accurate results

Looping functions allow for another performance improvement as well.  They allow you to declare state that is local to the test function, but outside the iteration loop, which can be used to alleviate the overhead of any global variable references you might have.

## Adaptive Test Cycles ##

JSLitmus adapts the iteration count for each test so that it will take about 1 second to run.  This happens automatically and the details how it works aren't that interesting.  But it's important to note that as part of determining the iteration count, JSLitmus will call your test functions more than once, even for looping test functions.  So be sure to initialize your test state so that your test functions behave consistently each time they are called.

## Charts ##

JSLitmus produces a chart showing the relative performance of all tests that have run.  This is updated any time the test data changes.  If your test file has a 

&lt;TITLE&gt;

 tag, the title will be included in the chart.  JSLitmus will also include the browser name and version, if detected.

Sharing charts is easy.  Simply share the chart URL/image as you would any other image in a webpage.  But, since the chart URL can get pretty long and ugly, you may find it convenient to click the "get tinyurl for chart" link to get a more compact version of the URL.

## Linking to this site ##

You are welcome to have your tests link directly to [JSLitmus.js](http://broofa.com/Tools/JSLitmus/JSLitmus.js) on this site, but please try to be considerate.  If you expect to be generating a lot (1000's) of requests/day please host your own version of this file.

Also, be aware that this site logs referrer information.  If you link to this site's version of the script, the URL for your test page will be recorded in my logs.

## Script Warnings in Internet Explorer ##

JSLitmus limits test times so that on most (non-IE) browsers you won't see the dreaded "Script is taking too long" dialog. Unfortunately on IE the situation is a bit ~~ridiculous~~ different.  Rather than limiting a script by time, [IE limits a script to 5M instructions](http://support.microsoft.com/default.aspx?scid=kb;en-us;175500).  This worked okay in 1997 when IE4 was all the rage and hardware was slower - scripts would run for 5-10 seconds before hitting this limit - but with modern hardware a CPU-intensive script like JSLitmus will trigger this in less than 500ms.

If you have a reasonably fast system you may run into these dialogs in IE, in which case the best solution is to modify your windows registry to increase the number of operations (I have mine set to 50,000,000).  The link above describes how to do this.