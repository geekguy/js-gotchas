# js-gotchas
JavaScript Gotchas and Common Mistakes

1. `0` is falsy but `"0"` is true.
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

2. JS handles null and undefined for date functions.
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
