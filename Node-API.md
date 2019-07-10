The CyberChef **Node.js** API provides most of CyberChef's operations with a **Node.js**-friendly interface, plus some other helpful functions.

For a taste of what operations are available in CyberChef, check out the [live demo][1].



## Features

- (Almost) all operations in the CyberChef web tool ([see exclusions](#excluded-operations))
- ES6 `import` and commonJS `require` capability
- Configurable, composable operations
- Import and run saved recipes from the CyberChef web tool with `chef.bake`


## Installation

```
npm install --save cyberchef
```



## Example usage

### Decode a Base64 encoded-string
```javascript
import chef from "cyberchef";

chef.fromBase64("U28gbG9uZyBhbmQgdGhhbmtzIGZvciBhbGwgdGhlIGZpc2gu").toString();
// => "So long and thanks for all the fish."
```


### Convert a date and time to a different time zone
```javascript
import chef from "cyberchef";

const aestTime = chef.translateDateTimeFormat("15/06/2015 20:45:00", {
    outputTimezone: "Australia/Queensland"
}).toString();
// => "Tuesday 16th June 2015 06:45:00 +10:00 AEST"
```

### Parse a Teredo IPv6 Address
```javascript
import chef from "cyberchef";

console.log(chef.parseIPv6Address("2001:0000:4136:e378:8000:63bf:3fff:fdd2"));
/** =>
Longhand:  2001:0000:4136:e378:8000:63bf:3fff:fdd2
Shorthand: 2001:0:4136:e378:8000:63bf:3fff:fdd2

Teredo tunneling IPv6 address detected

Server IPv4 address: 65.54.227.120
Client IPv4 address: 192.0.2.45
Client UDP port:     40000
Flags:
        Cone:    1 (Client is behind a cone NAT)
        R:       0
        Random1: 0000
        UG:      00
        Random2: 00000000

This is a valid Teredo address which complies with RFC 4380, however it does not comply with RFC 5991 (Teredo Security Updates) as there are no randomised bits in the flag field.

Teredo prefix range: 2001::/32 */
```

### Convert data from a Hexdump, then decompress
```javascript
import chef from "cyberchef";

const message = new chef.Dish(`00000000  1f 8b 08 00 12 bc f3 57 00 ff 0d c7 c1 09 00 20  |.....¼óW.ÿ.ÇÁ.. |
00000010  08 05 d0 55 fe 04 2d d3 04 1f ca 8c 44 21 5b ff  |..ÐUþ.-Ó..Ê.D![ÿ|
00000020  60 c7 d7 03 16 be 40 1f 78 4a 3f 09 89 0b 9a 7d  |\`Ç×..¾@.xJ?....}|
00000030  4e c8 4e 6d 05 1e 01 8b 4c 24 00 00 00           |NÈNm....L$...|`)
.apply(chef.fromHexdump)
.apply(chef.gunzip)
.toString();
// => "So long and thanks for all the fish."
```

### Interact with files from Node.js `fs` library
```javascript
import chef from "cyberchef";

fs.writeFileSync("test.txt", chef.toHex("hello").toString());

const file = new chef.Dish(fs.readFileSync("test.txt"));
console.log(file) ;
// => 68 65 6c 6c 6f

file.apply(chef.fromHex).toString() 
// => hello;
```

## Import with ES6 `import` or CommonJS `require`

### ES6 imports
You can import the default `chef` object:

```javascript
import chef from "cyberchef";
chef.toMorseCode("hello").toString();
// => .... . .-.. .-.. ---
```

#### Named imports
You can import specific operations using a deep import specifier:
```javascript
import { toHex } from "cyberchef/src/node/index.mjs";
toHex("Menu a la carte");
// => 4d 65 6e 75 20 61 20 6c 61 20 63 61 72 74 65
```

### CommonJS require

```javascript
const chef = require("cyberchef");
chef.toKebabCase("Large chicken shish, garlic mayo, no salad.").toString();
// => large-chicken-shish-garlic-mayo-no-salad
```

## Usage

> ### Operation names
> Operation names in the CyberChef API are camelCase versions of the operations on the web app, except for when the operation name begins with more than one uppercase character. For example, "Zlib Deflate" becomes `zlibDeflate`, but "SHA2" stays as `SHA2`.


### Operations

You can use operations with default config, or define your own arguments.

> To see what values an argument accepts, either refer to the UI or use `chef.help(<operation name>)` and inspect the  properties.

Operation with default args:
```javascript
chef.toCharcode("Service!") // Space delimiter, Base 16
// => 53 65 72 76 69 63 65 21
```

Configurable args:
> Arg keys are camelCase versions of those displayed on the CyberChef UI.
```javascript
chef.toCharcode("Service!", {
    base: 8
    // delimiter stays as default
});
// => 123 145 162 166 151 143 145 41
```

Where arguments are lists of options, they are case sensitive. It is recommended to use the `args` property of the operation to access options. For example:
```javascript
// OK
chef.convertDistance(122, {
    inputUnits: "Kilometers (km)",
    outputUnits: "Cars (4m)"
});
// => 30500

// Less error-prone
chef.convertDistance(122, {
    inputUnits: chef.convertDistance.args.inputUnits.options[5],
    outputUnits: chef.convertDistance.args.outputUnits.options[17]
})
// => 30500

// Invalid outputUnits value
chef.convertDistance(122, {
    inputUnits: "Kilometers (km)",
    outputUnits: "cars"
});
// => NaN
```


#### Operation input
Operations accept a wide range of input types, such as strings, numbers, byteArrays and ArrayBuffers. For most cases, you can throw any input type at an operation and it will deal with it. 

If the given input to an operation is not the operation's input type, it will attempt to convert the input before operating on it. For example, `toBase32`'s input type is `byteArray`, so if you input a string, it converts that string into a byte array before running the operation.

Files from `fs` can be read into appropriate functions in chef. They are converted into `ArrayBuffer`s.

JavaScript objects are accepted as input to operations that expect JSON, for example, `JSONBeautify`.

#### Operation arguments
Operation arguments are the second argument in an operation call. The best way to see an operation's arguments is to use `chef.help(<argument name here>)` or consult the web UI.

Some operation arguments have default values. Some, like `AESEncrypt` for example, will not work without specifying some arguments.

##### Number, String, binaryString and boolean arguments
Declare these arguments as key:value pairs, where the key is the argument name.

Example: `toBase` (Number)
```javascript
chef.toBase("45", {
    radix: 8
})
// => 55
```

Example: `toBase32` (binaryString)
```javascript
chef.toBase32("diamond", {
    alphabet: "A-Z",
});
// => MRUWCLPNZSA
```

##### Option arguments
These are arguments represented as dropdowns in the UI. Here the string value must be an exact match for the option string. For convenience, it is recommended to use the `arg` property of the operation.

Example: `toDecimal`
```javascript
chef.toDecimal("Hello", {
    delimiter: "Colon", // note case sensitive
});
// => 72:101:108:108:111

// Equivalent and less error-prone:
chef.toDecimal("Hello", {
    delimiter: chef.toDecimal.args.delimiter.options[3],
});
// => 72:101:108:108:111
```

##### toggleString arguments
ToggleStrings are arguments where there is a value and an option in one argument. For example, the `ADD` operation has a `key` argument where you can also select the encoding for the key.

Example: `ADD` with default encoding
```javascript
chef.ADD("abc", {
    key: "123",
});
```

Example: `ADD` with explicit encoding. Note the property `toggleValues` rather than `options` in the `args` object.
```javascript
chef.ADD("abc", {
    key: {
        string: "123",
        option: chef.ADD.args.key.toggleValues[4],
    },
});
```


#### Operation return type
Operations return a [`Dish`](#the-dish-type) type. [`Dish`](#the-dish-type) has some functions and behaviours that make it easy to use. Most of the time you can ignore this and treat it as a string or a number.

When logging to the console, string coercion and number coercion work as you would expect:

```javascript
const result = chef.toBase32(32).apply(chef.fromBase32);

// Logging to console
console.log(result); // => 32

// Number coercion
assert.equal(result + 3, 35); // OK

// String coercion
assert.equal(result.toString(), "32"); // OK
```

The `value` property of an operation result will give the raw result.  For example:
```javascript
// dropBytes has output type ArrayBuffer
chef.dropBytes("I'd love a byte").value
// => ArrayBuffer { byteLength: 10 }
```

Calling `toString` will give a string representation of the result.

[See more about `Dish` here.](#the-dish-type)

##### `File` output type from Dish
Node.js does not have a `File` API, so for operations that return a `File`, we use a shim which creates an object with the same shape as the `File` Web API. You can access the underlying data if this file type with `file.data`, which will be an `ArrayBuffer`.

#### Compose operations
Operations can be composed using `apply`. This example performs ROT13 substitution followed by conversion to Hex on some input:
```javascript
chef.ROT13("Medium rare, please.").apply(chef.toHex).toString();
// => 5a 72 71 76 68 7a 20 65 6e 65 72 2c 20 63 79 72 6e 66 72 2e
```


#### Excluded operations
The best way to browse operations is to run CyberChef, either locally or via the [demo][1].

Most CyberChef operations are included in the Node.js API, with the exception of Flow Control operations. Excluded operations will throw an `ExcludedOperationError` if called.

For a full list of excluded operations, see the source files under [`src/node/config/excludedOperations.mjs`](https://github.com/gchq/CyberChef/blob/master/src/node/config/excludedOperations.mjs).


## Helper functions

The `chef` object has some other helpful functions.

#### `help`
The `help` function returns the description of an operation. Use this to see what input, output and arguments an operation has.

```javascript
chef.help(chef.toBase32);
/**
[{ module: 'Default',
  description: 'Base32 is a notation for encoding arbitrary byte data using a restricted set of symbols that can be conveniently used by humans and processed by computers. It uses a smaller set of characters than Base64, usually the uppercase alphabet and the numbers 2 to 7.',
  inputType: 'byteArray',
  outputType: 'string',
  flowControl: false,
  args:
   [ { name: 'Alphabet',
       type: 'binaryString',
       value: 'A-Z2-7=',
       toggleValues: [] } ],
  name: 'To Base32' }]
*/
```

`help` also accepts the name of an operation as a string, ignoring case and whitespace:
```javascript
chef.help("to base 32") // OK
chef.help("tobase32")   // OK
chef.help("toBase32")   // OK
```

If there is more than one match for the given search term, `help` will return multiple matches. It searches in the operation name and the description field, prioritising matches in the operation name.

#### `bake`
`bake` is useful for building "recipes" - chains of operations to apply to some input, in order. `bake` accepts operations in multiple ways. It always returns a `Dish` object.

One operation, no arguments
```javascript
chef.bake("I'll have the cod.", chef.toBase64);
// => SSdsbCBoYXZlIHRoZSBjb2Qu
```
One operation by name
```javascript
chef.bake("I'll have the cod.", "to base 64");
// => SSdsbCBoYXZlIHRoZSBjb2Qu
```
Multiple operations, by name or by function (default arguments)
```javascript
chef.bake("I'll have the cod", [chef.toBase64, "sha1"]);
// => f20135964d60f5fb66f971f5ee33d8395d1f90bf
```

One operation, with custom arguments
```javascript
chef.bake("I'll have the salmon.", {
    op: chef.toBase64,
    args: {
        alphabet: "A-Z",
    },
});
// => SSCBYXZIHRZSBYW
```

Multiple operations with custom arguments
```javascript
chef.bake("I'll have the salmon.", [
    {
        op: chef.toBase64,
        args: {
            alphabet: "A-Z"
        }
    },
    {
        op: chef.sort,
        args: {
            delimiter: "Nothing (separate chars)",
            reverse: true,
        }
    }
]);
// => ZZYYXWSSSRIHCBB
```

> ### Using recipes from the CyberChef web app
> `chef.bake` is compatible with the JSON format from saved recipes in the CyberChef web UI. Therefore, you can use the UI to figure out your recipe, and then export it to use in the Node.js API.

```javascript
//  taken from CyberChef web app 'Save Recipe' export
const recipe = [
    { "op": "To Base64",
      "args": ["A-Z"] },
    { "op": "Sort",
      "args": ["Nothing (separate chars)", true, "Alphabetical (case sensitive)"] }
];

chef.bake("I'll have the salmon.", recipe);
// => ZZYYXWSSSRIHCBB

```


## The `Dish` type
All operations in CyberChef return the `Dish` type. This allows operations to be composed. You can also create a new `Dish` with some input and then perform operations on it.

Example:
```javascript
import chef from "cyberchef";
const dish = new chef.Dish("hello");
dish.apply(chef.toBase32).toString();
// => NBSWY3DP
```

For more information on `Dish`, see the [`Dish` API](#Dish) below

#### `Dish` coercion
Dish will coerce to a String or a Number where appropriate.

#### `Dish` examples

Empty Dish contructor
```javascript
const dish = new chef.Dish();
dish.value; // => ArrayBuffer {byteLength: 0}
dish.type; // => 4 (ArrayBuffer)
```

Dish with type implied from input
```javascript
const dish = new chef.Dish("input");
dish.value; // => "input"
dish.type; // => 1 (string)
```

Dish with explicit type declaration
```javascript
const dish = new chef.Dish(2, "number");
dish.value; // => 2
dish.type; // => 2 (number)
```

Dish will not perform coercion on construction
```javascript
const dish = new chef.Dish("2", "number");
// Data is not a valid number: "2"
```

Coerce dish value to another type
```javascript
const dish = new chef.Dish("42");
dish.type; // => 1 (string)
dish.get("number") // => 42
dish.type; // => 2 (number)
```

Immutably present dish value as another type with `presentAs`
```javascript
const dish = new chef.Dish("42");
dish.type; // => 1 (string)
dish.presentAs("number") // => 42
dish.type; // => 1 (string)

```

Dish values get coerced to String on `console.log` or `toString`
```javascript
const result = chef.ADD("some input", {
    key: "65 66 67 68",
});
// ADD output type is byteArray.
result.value // => [ 216, 213, 212, 205, 133, 207, 213, 216, 218, 218 ]
console.log(result); /* => ØÕÔÍÏÕØÚÚ */
result.toString(); /* => ØÕÔÍÏÕØÚÚ */
```

Perform operations straight off a Dish:
```javascript
const result = new chef.Dish("Yum").apply(chef.toBinary);
result.value; // => 01011001 01110101 01101101
result.type; // => 1 (string)
```

#### `Dish` types
The types for `Dish` are:
- byteArray
- string
- number
- html
- ArrayBuffer
- BigNumber
- JSON

## Roadmap
To see what features and fixes are in the pipeline, refer to the repository [issues][4]. 


# REPL

To run the repl, install CyberChef, run `npm run repl`.

# API

## chef

### `chef.help(searchTerm)`
Returns configuration for operations matching the search term.

#### Arguments:
|Name|Type|Description|
|-|-|-|
|searchTerm|String or operation|Either a string to search for, like "base 64", or an operation.|

#### Example:
```javascript
chef.help("md5")
/** => 
1 result found.
[ { module: 'Crypto',
    description:
     'MD5 (Message-Digest 5) is a widely used hash function. It has been used in a variety of security applications and is also commonly used to check the integrity of files.<br><br>However, MD5 is not collision resistant and it isn\'t suitable for applications like SSL/TLS certificates or digital signatures that rely on this property.',
    infoURL: 'https://wikipedia.org/wiki/MD5',
    inputType: 'ArrayBuffer',
    outputType: 'string',
    flowControl: false,
    manualBake: false,
    args: [],
    name: 'MD5' } ]
*/
```

### `chef.Dish`
Class which is used to return operation results, or instantiate your own for operation composition

Also exposed as a top level export `Dish`.
#### Arguments:
|Name|Type|Description|
|-|-|-|
|`inputOrDish=null`|Buffer, ArrayBuffer, String, Number, Object or Dish|The input on which you want to operate, or a Dish instance to clone a dish. If `null`, the dish's `value` and `type` properties are set to `[]` and `4` respectively.|
|`type=null`|`String` or `Dish` enum|The type that you want the input coerced to.|
#### Example:
```javascript
const dish = new chef.Dish("hello");
dish.value; // "hello"
dish.type; // 1 (signifying string type enum)


// Explicitly declare type
const dish = new chef.Dish(5, "number");
dish.value; // 5
dish.type; // 2 (number)
```

### `chef.bake`
Perform an array of operations, in order, on the given input. Return a `Dish` of the result.

Useful for consuming recipes saved from the web version of CyberChef.

#### Arguments:
|Name|Type|Description|
|-|-|-|
|input|any|The input for the recipe.|
|recipeConfig|`String` or operation or an object with properties `op` and `args` (see CyberChef save recipe JSON format), or an `Array` of one or more of these types.|A description of which operations, with given arguments, to perform on the input.
#### Examples:
```javascript
// Single operation input
chef.bake("Apple pie", chef.toBase32).toString();
// => IFYHA3DFEBYGSZI=

// Single operation name
chef.bake("Apple pie", "to base 32").toString();
// => IFYHA3DFEBYGSZI=

// Single operation object with args
chef.bake("Apple pie", {
    op: chef.toBase32, // string would work here too
    args: {
        alphabet: "A-Z"
    }
}).toString();
// => IFYHADFEBYGSZI

// Multiple operations
chef.bake("Apple pie", [
    chef.toBase32,
    {
        op: "from base 32",
        args: {
            alphabet: "A-Z2-7=",
        },
    }
]).toString();
// => Apple pie
```


## `Dish`


### static `typeEnum`
Map from string representation of type to the type enum.
#### Arguments
|Name|Type|Description|
|-|-|-|
|typeStr|String|String representation of type|

#### Returns 
|Type|Description|
|-|-|
|Number|The type enum for the string.

#### Example
```javascript
chef.Dish.typeEnum("bytearray"); 
// => 0
```

### static `enumLookup`
Get string representation of Dish type enum.

#### Arguments
|Name|Type|Description|
|-|-|-|
|typeEnum|Number|Dish type enum|

#### Returns 
|Type|Description|
|-|-|
|String|The string representation of the Dish type enum.

#### Example
```javascript
chef.Dish.enumLookup(Dish.JSON); 
// => "JSON"
```

### Constructor
Construct a new Dish.
#### Arguments:
|Name|Type|Description|
|-|-|-|
|dishOrInput|any or `Dish` instance|The input for the recipe. Input a Dish to clone it. Accepts object of shape `{value: [various], type: [number]}` or just input.|
|type|String or Number|type enum or string representation of type.

#### Examples:
```javascript
// Empty Dish constructor
const dish = new chef.Dish();

// String type dish (implied)
const dish = new chef.Dish("some input");

// Number type dish (explicit)
const dish = new chef.Dish(460, "number");

// Dish from another dish. Clones it.
const secondDish = new chef.Dish(dish);
```

### `dish.apply`
A method for chaining operations. Run the given operation with the dish as the input. A new dish is returned and the original is not changed.

#### Arguments
|Name|Type|Description|
|-|-|-|
|operation|Operation|Operation to run with dish as input|
|args=null|Object| Operation arguments|

#### Examples
```javascript
// Operation with default arguments
const dish = new chef.Dish(5, "number");
const result = dish.apply(chef.toBase32);

// Operation with custom arguments
const base64 = dish.apply(chef.toBase64, {
    alphabet: "A-Z",
});
```


### `dish.get` or `dish.to`
Coerce the dish to a different type. Mutable.

#### Arguments
|Name|Type|Description|
|-|-|-|
|type|String or Number|Type enum or string representation of type to convert to|

#### Returns 
|Type|Description|
|-|-|
|any|The value of the dish after being coerced to the new type.

#### Examples
```javascript
const dish = new Dish("360");

dish.type; // => 1 (string)

const asNumber = dish.get("number");

asNumber; // => 360

dish.type; // => 2 (number)
```

### `dish.presentAs`
return the dish value as another type, without changing the dish. 

Same arguments as `dish.get`.


### `toString`
Get the current dish value as a string. Does not change the dish.


### `dish.set`
Sets the data value and type and then validates them.
#### Arguments
|Name|Type|Description|
|-|-|-|
|value|*|The new dish value|
|type|String or `Dish` enum| The data type of value. See [Dish enums](#dish-enums).

#### Example
```javascript
const dish = new Dish()

dish.set("this is the value", "string"); // ok

dish.set("this has invalid type", "number") // throws DishError.
```

### `dish.valid`

#### Returns
|Type|Description|
|-|-|
|boolean|whether the current dish config is valid.

### `dish.size`
Calculate the size of the dish.
#### Returns 
|Type|Description|
|-|-|
|Number|A representation of the size of the dish value. Calculated in different ways depending on the type.

### `dish.clone`
Clone the dish.
#### Returns 
|Type|Description|
|-|-|
|Dish|The cloned dish.



### Dish enums

The Dish enums are there to describe the Dish types.

```javascript
Dish.BYTE_ARRAY     = 0;
Dish.STRING         = 1;
Dish.NUMBER         = 2;
Dish.HTML           = 3;
Dish.ARRAY_BUFFER   = 4;
Dish.BIG_NUMBER     = 5;
Dish.JSON           = 6;
Dish.FILE           = 7;
Dish.LIST_FILE      = 8;
```

[1]:https://gchq.github.io/CyberChef
[2]:https://github.com/gchq/CyberChef
[3]:https://developer.mozilla.org/en-US/docs/Web/API/File
[4]:https://github.com/gchq/CyberChef/issues
