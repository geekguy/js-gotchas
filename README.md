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

2. NaN is not a NaN.
      ```js
      NaN === NaN   // false
      ```
    During the evaluation of a tripple equals `a === b` following things are considered. FYI `typeof NaN` is `number`
    - If `typeof a` is different from `typeof b`, return `false`.
    - If `typeof a` is `number`, then
      - If a is NaN, return `false`.
      - If b is NaN, return `false`.