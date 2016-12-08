## How to add an operation

 1. Create a new file in the `src/js/operations` directory and name it using CamelCase. e.g. `MyOperation.js`
 2. In this file, create a namespace with the same name and populate it with a single function looking like this (all function and variable names should be written in underscore_lower_case):
    
        var MyOperation = {
            run_my_operation: function (input, args) {
                return input;
            }
        };

    - `input` will be the input data passed on from the previous operation (or the data entered by the user if yours is the first operation). Its data type is specified in the next step by `input_type`.
    - `args` will be an array of the arguments for your operation. They are specified in the next step by `args`.
    - Make sure that you return the output data in the format specified in the next step by `output_type`.

 3. In `src/js/config/OperationConfig.js`, create a new entry. For example:

        "The name of your operation": {
            description: "A short description if necessary, optionally containing HTML code (e.g. lists and paragraphs)",
            run: MyOperation.run_my_operation, // a reference to the function that runs your operation 
            input_type: "byte_array", // the input type for your operation, see the next section for valid types
            output_type: "byte_array", // the output type for your operation, see the next section for valid types
            highlight: true, // [optional] true if the operation does not change the position of bytes in the output (so that highlighting can be calculated)
            highlight_reverse: true, // [optional] same as above but for the reverse of the operation (output to input highlighting)
            manual_bake: false, // [optional] true if auto-bake should be disabled when this operation is added to the recipe
            args: [ // A list of the arguments that the user will be presented with
                {
                    name: "Argument name",
                    type: "string", // the argument data type, see the next section for valid types
                    value: MyOperation.DEFAULT_VALUE // the default value of the argument
                }
            ]
        }
        
    For example:
    
        "XOR": {
            description: "XOR the input with the given key, provided as either a hex or ASCII string.<br>e.g. fe023da5<br><br><b>Options</b><br><u>Null preserving:</u> If the current byte is 0x00 or the same as the key, skip it.<br><br><u>Differential:</u> Set the key to the value of the previously decoded byte.",
            run: BitwiseOp.run_xor,
            input_type: "byte_array",
            output_type: "byte_array",
            args: [
                {
                    name: "Key",
                    type: "binary_string",
                    value: ""
                },
                {
                    name: "Key format",
                    type: "option",
                    value: BitwiseOp.KEY_FORMAT
                },
                {
                    name: "Null preserving",
                    type: "boolean",
                    value: BitwiseOp.XOR_PRESERVE_NULLS
                },
                {
                    name: "Differential",
                    type: "boolean",
                    value: BitwiseOp.XOR_DIFFERENTIAL
                }
            ]
        }
        
 4. In `src/js/config/Categories.js`, add your operation name to an appropriate list. This determines which menu it will appear in. You can add it to multiple menus if you feel it is appropriate.
 5. Finally, run `grunt dev` if you haven't already. If it's already running, it should automatically build a development version when you save the files.
 6. You should now be able to view your operation on the site by browsing to `build/dev`.
 7. You can write whatever code you like as long as it is encapsulated within the namespace you created (`MyOperation`). Take a look at `src/js/operations/Entropy.js` for a good example.
 8. You may find it useful to use some helper functions which have been written in `src/js/core/Utils.js`. These are available in the `Utils` object (e.g. `Utils.str_to_byte_array("Hello")` returns `[72,101,108,108,111]`).
 

## Data types

**Input and Output**

Four data types are supported for the input and output of operations:

 1. `string` - e.g. `"hello"`
 2. `byte_array` - e.g. `[104,101,108,108,111]`
 3. `number` - e.g. `562` or `3.14159265`
 4. `html` - e.g. `"<p>hello</p>"`
 
Each operation can define any of these data types as their input or output. The data will be automatically converted to the specified type before running the operation.

**Ingredients**

Operation arguments (ingredients) can be set to any of the following types:

 1. `string` or `short-string`
     - e.g. `"hello"`
     - A `short-string` will simply display a smaller input box.
 2. `binary_string` or `binary-short-string`
     - e.g. `"hello\nworld"`
     - Escaped characters entered by the user will be automatically converted to the bytes they represent. A simple `string` type will return `"hello\\nworld"` in the above case.
 3. `text`
     - User is given a textbox for free-flow text.
 4. `byte_array`
     - e.g. user inputs `"68 65 6c 6c 6f"`, operation receives `[104,101,108,108,111]`.
 5. `number`
     - e.g. `562`
     - This can handle both integer and float values.
 6. `boolean`
     - User is presented with a checkbox, operation receives `true` or `false`.
 7. `option`
     - Given an array of strings, the user is presented with a dropdown selection box with each of those strings as an option. The selected string is sent to the operation.
 7. `populate_option`
     - Given an array of `{name: "", value: ""}` objects, the user is presented with a dropdown selection box with the names as options. The corresponding value will be assigned to whichever argument index the `target` parameter is set to.
     - See the *Regular expression* configuration in `src/js/config/OperationConfig.js` for an example of how this works.
 8. `editable_option`
     - Given an array of `{name: "", value: ""}` objects, the user is presented with an editable dropdown menu. The items in the dropdown are labelled with `name` and set the argument to `value` when selected.
 9. `toggle_string`
     - User is presented with a string input box with a toggleable dropdown attached.
     - Populate the dropdown using the `toggle_values` property.
     - Operation receives an object with two properties: `option` containing the user's dropdown selection, and `string` containing the input box contents.
     - Particularly useful for arguments that can be specified in various different formats.
     - See the *XOR* configuration in `src/js/config/OperationConfig.js` for an example of how this works.
