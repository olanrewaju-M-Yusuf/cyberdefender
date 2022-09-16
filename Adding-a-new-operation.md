## How to add an operation

The easiest way to create a new operation is to use the provided quickstart script. This can be run using the command `npm run newop`. This script will walk you through the configuration process and create your operation file in the `src/core/operations` directory.
        
Once this file has been created, add your operation to the [`src/core/config/Categories.json`](https://github.com/gchq/CyberChef/blob/master/src/core/config/Categories.json) file. This determines which menu it will appear in. You can add it to multiple menus if you feel it is appropriate.
        
Finally, run `npm start` if you haven't already. If it's already running, it should automatically build a development version when you save the files. You should now be able to view your operation on the site by browsing to [`localhost:8080`](http://localhost:8080).

You can write whatever code you like as long as it is encapsulated within the object you created. Take a look at [`src/core/operations/Entropy.mjs`](https://github.com/gchq/CyberChef/blob/master/src/core/operations/Entropy.mjs) for a good example.

You may find it useful to use some helper functions which have been written in [`src/core/Utils.mjs`](https://github.com/gchq/CyberChef/blob/master/src/core/Utils.mjs) (e.g. `Utils.strToByteArray("Hello")` returns `[72,101,108,108,111]`).
 

## Data types

**Input and Output**

Nine data types are supported for the input and output of operations:

 1. `string` - e.g. `"hello"`
 2. `byteArray` - e.g. `[104,101,108,108,111]`
 3. `number` - e.g. `562` or `3.14159265`
 4. `html` - e.g. `"<p>hello</p>"`
 5. [`ArrayBuffer`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) - e.g. `new Uint8Array([104,101,108,108,111]).buffer`
 6. `BigNumber` - e.g. `12345678901234567890`
 7. `JSON` - e.g. `[{"a":1,"b":2}]`
 8. [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File) - e.g. `new File()`
 9. `List<File>` - e.g. `[new File(), new File()]`
 
Each operation can define any of these data types as their input or output. The data will be automatically converted to the specified type before running the operation.

**Ingredients**

Operation arguments (ingredients) can be set to any of the following types:

 1. `string` or `shortString`
     - e.g. `"hello"`
     - A `shortString` will simply display a smaller input box.
 2. `binaryString` or `binaryShortString`
     - e.g. `"hello\nworld"`
     - Escaped characters entered by the user will be automatically converted to the bytes they represent. A simple `string` type will return `"hello\\nworld"` in the above case.
 3. `text`
     - User is given a textbox for free-flow text.
 4. `byteArray`
     - e.g. user inputs `"68 65 6c 6c 6f"`, operation receives `[104,101,108,108,111]`.
 5. `number`
     - e.g. `562`
     - This can handle both integer and float values.
 6. `boolean`
     - User is presented with a checkbox, operation receives `true` or `false`.
 7. `option`
     - Given an array of strings, the user is presented with a dropdown selection box with each of those strings as an option. The selected string is sent to the operation.
     - You can use the `defaultIndex` property to define which index should be selected by default, if required.
 7. `populateOption`
     - Given an array of `{name: "", value: ""}` objects, the user is presented with a dropdown selection box with the names as options. The corresponding value will be assigned to whichever argument index the `target` parameter is set to.
     - See the *Regular expression* configuration in `src/core/config/OperationConfig.js` for an example of how this works.
 8. `editableOption` or `editableOptionShort`
     - Given an array of `{name: "", value: ""}` objects, the user is presented with an editable dropdown menu. The items in the dropdown are labelled with `name` and set the argument to `value` when selected.
     - You can use the `defaultIndex` property to define which index should be selected by default, if required.
 9. `toggleString`
     - User is presented with a string input box with a toggleable dropdown attached.
     - Populate the dropdown using the `toggleValues` property.
     - Operation receives an object with two properties: `option` containing the user's dropdown selection, and `string` containing the input box contents.
     - Particularly useful for arguments that can be specified in various different formats.
     - See the *XOR* configuration in `src/core/config/OperationConfig.js` for an example of how this works.


## Presenting complex data

The output of your operation will be passed on to the next operation in the recipe, or to the Output field if it is the final operation. If your operation has a complex output, it should be presented to the user in a friendly format, perhaps using HTML markup, however this format should not be sent to follow-on operations, as it would make onward processing unnecessarily complex.

In these situations, the `present` function should be used. This function is called if your operation is the final operation in the recipe. It is passed the output of your `run` function which you can then manipulate into a suitable format for displaying to the user. This allows you to return a sensible format which can be easily processed from your `run` function.

The data type for your present function should be specified in the operation constructor using `this.presentType`.

A good example of this can be found in [`src/core/operations/Unzip.mjs`](https://github.com/gchq/CyberChef/blob/master/src/core/operations/Unzip.mjs).