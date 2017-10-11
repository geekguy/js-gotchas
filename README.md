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

### 6. Comparing null and zero

```js
console.log(null == 0); // false
console.log(null >  0); // false
console.log(null >= 0); // true
```
>What really happens is that the *Greater-than-or-equal Operator*  (`>=`), performs type coercion ([`ToPrimitive`](http://www.ecma-international.org/ecma-262/5.1/#sec-9.1)), with a >*hint* type of `Number`, actually all the relational operators have this behavior.
>
>`null` is treated in a special way by the *Equals Operator* (`==`). In a brief, it only *coerces* to `undefined`:
>
>    null == null; // true
>    null == undefined; // true
>
>Value such as `false`, `''`, `'0'`, and `[]` are subject to numeric type coercion, all of them coerce to zero.
>
>You can see the inner details of this process in the [The Abstract Equality Comparison Algorithm](http://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3) and [The Abstract Relational Comparison Algorithm](http://www.ecma-international.org/ecma-262/5.1/#sec-11.8.5).
>
>In Summary: 
>
>* Relational Comparison: if both values are not type String, `ToNumber` is called on both. This is the same as adding a `+` in front, which for null coerces to `0`. 
>
>* Equality Comparison: only calls `ToNumber` on Strings, Numbers, and Booleans.
>
([source](https://stackoverflow.com/a/2910608/100184))
