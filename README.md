# js-gotchas
JavaScript Gotchas and Common Mistakes

### 1. `0` is falsy but `"0"` is true.

```js
if (0) {
    console.log('yey.');
  } else {
    console.log('boom'); // this will be printed
  }


if ("0") {
    console.log('yey.'); // this will be printed
  } else {
    console.log('boom');
  }
```


 Why?

 Because of coercion. Javascript sees a string and like a benovalent language that Javascript is, it tries to fix this itself. Now if condition needs a Boolean output, So compiler tries to convert a string into boolean operator.
 This is how toBoolean is supposed to work as per Sec 7.1.2 of [ECMA-262](http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf)
  > The result is false if the argument is the empty String (its length is zero); otherwise the result is true.

  So Empty string will return false and rest everything will return true.

 ### 2. MIN_VALUE and MAX_VALUE
 ```js
 console.log(Number.MAX_VALUE > 0); // true

 console.log(Number.MIN_VALUE < 0); //false GOTCHA
 ```

People might assume that if MAX_VALUE is the greatest positive number, MIN_VALUE will contain the greatest negative number. Wrong. MIN_VALUE contains the smallest positive number. It should have been ideally named as MIN_POSITIVE_VALUE.


### 3. NaN is not a NaN.
```js
NaN === NaN   // false
```
    During the evaluation of a tripple equals `a === b` following things are considered. FYI `typeof NaN` is `number`
    - If `typeof a` is different from `typeof b`, return `false`.
    - If `typeof a` is `number`, then
      - If a is NaN, return `false`.
      - If b is NaN, return `false`.

### 4. JS handles null and undefined differently for date functions.
```js
let a = new Date(undefined);
console.log(a);         // prints "Invalid Date"

let b = new Date(null);
console.log(b);         // prints "Thu Jan 01 1970 05:30:00 GMT+0530 (IST)"  -> Epoch time 0

let c = new Date();
console.log(c);         // prints "Sat Oct 07 2017 18:07:52 GMT+0530 (IST)" -> Current time

let d = new Date(false);
console.log(d);         // prints "Thu Jan 01 1970 05:30:00 GMT+0530 (IST)" -> Epoch time 0

let e = new Date(true);
console.log(e);         // prints "Thu Jan 01 1970 05:30:00 GMT+0530 (IST)" -> Still epoch time 0

let f = new Date(0);
console.log(f);         // prints "Thu Jan 01 1970 05:30:00 GMT+0530 (IST)" -> Again epoch time 0
```
   Although when no parameters are passed to a function as argument, its value is assigned undefined. But in the case of Date constructor these cases are treated differently. Some internal logic inside Date() constructor but can cause lot of unexpected results if someone uses
```js
function printDate(param1){
  console.log(new Date(param1));
}
printDate(object.scheduledDate);
```
   In this case if we expect that if ``` object.scheduledDate ``` is ``` undefined ``` then print current date or else print the scheduled date. But this *js-gotcha* will give unexpected result and you will loose half of your hair.

### 5. Load scripts in order of dependency requirements. 
The simplified explanation of this issue is that a variable or function cannot be referenced or called prior to being defined elsewhere in the code. Where scripts require other scripts, or content requires a particular loading order, this may become more complex. In the following example, script1.js is loaded after the testVar1 function executes, thus varInScript1 is not defined and errors in the browser. 

    ``` 
    <html>
      <head>        
        <script type="text/javascript">
          function testVar1() {
              if (varInScript1) {
                console.log("Variable found in script1");
              }
          }
          window.onload = testVar1;
        </script>
      </head>
    <body>
    </body>
    <script type="text/javascript" src="script1.js">
    </html>
    ```

By extrapolating this use case into more complicated environments, implementing dependency handling solutions, such as RequireJS, may assist to track and handle dependency requirements.

### 6. Confusing "undefined" and "null"
In other languages, you can set null to variables. JavaScript uses undefined and null, but it defaults variables to undefined and objects to null. These values are assigned when an incorrect calculation is made or you assign a null reference to an object. For an object to be null, it must be defined, so you must be careful when you compare objects.

The following code will give you an error if the object is undefined:
        ```
        if (object !== null && typeof object !== "undefined")
        ```
Always check if the object is undefined first, then identify if it’s null.
        ```
        if (typeof object !== "undefined" && object!== null)
        ```
### 7. Incorrect use of function definitions inside for loops

Consider this code:
        ```
        var elements = document.getElementsByTagName('input');
        var n = elements.length;    // assume we have 10 elements for this example
        for (var i = 0; i < n; i++) {
            elements[i].onclick = function() {
                console.log("This is element #" + i);
            };
        }
        ```
Based on the above code, if there were 10 input elements, clicking any of them would display “This is element #10”! This is because, by the time onclick is invoked for any of the elements, the above for loop will have completed and the value of i will already be 10 (for all of them).

Here’s how we can correct the above code problems, though, to achieve the desired behavior:
        ```
        var elements = document.getElementsByTagName('input');
        var n = elements.length;    // assume we have 10 elements for this example
        var makeHandler = function(num) {  // outer function
             return function() {   // inner function
                 console.log("This is element #" + num);
             };
        };
        for (var i = 0; i < n; i++) {
            elements[i].onclick = makeHandler(i+1);
        }
        ```
In this revised version of the code, makeHandler is immediately executed each time we pass through the loop, each time receiving the then-current value of i+1 and binding it to a scoped num variable. The outer function returns the inner function (which also uses this scoped num variable) and the element’s onclick is set to that inner function. This ensures that each onclick receives and uses the proper i value (via the scoped num variable).
