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
2. Load scripts in order of dependency requirements. The simplified explanation of this issue is that a variable or function cannot be referenced or called prior to being defined elsewhere in the code. Where scripts require other scripts, or content requires a particular loading order, this may become more complex. In the following example, script1.js is loaded after the testVar1 function executes, thus varInScript1 is not defined and errors in the browser. 

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