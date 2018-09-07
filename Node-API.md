The CyberChef **Node.js** API provides most of CyberChef's operations with a **Node.js**-friendly interface, plus some other helpful functions.

For a taste of what operations are available in CyberChef, check out the [live demo][1].



## Features

- (Almost) all operations in the CyberChef web tool ([see exclusions](#excluded-operations))
- ES6 `import` and commonJS `require` capability
- Configurable, composable operations
- Import and run saved recipes from the CyberChef web tool with `chef.bake`


## Installation

```
npm install --save CyberChef
```



## Example usage

### Decode a Base64 encoded-string
```javascript
import chef from "CyberChef";

chef.fromBase64("U28gbG9uZyBhbmQgdGhhbmtzIGZvciBhbGwgdGhlIGZpc2gu").toString() === "So long and thanks for all the fish."; // true
```


### Convert a date and time to a different time zone
```javascript
import chef from "CyberChef";

const aestTime = chef.translateDateTimeFormat("15/06/2015 20:45:00", {
    outputTimezone: "Australia/Queensland"
});
aestTime.toString() === "Tuesday 16th June 2015 06:45:00 +10:00 AEST"; //true
```

### Parse a Teredo IPv6 Address
```javascript
import { parseIPv6Address } from "CyberChef";

console.log(parseIPv6Address("2001:0000:4136:e378:8000:63bf:3fff:fdd2"));
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
import { Dish } from "CyberChef";

const message = new Dish(`00000000  1f 8b 08 00 12 bc f3 57 00 ff 0d c7 c1 09 00 20  |.....¼óW.ÿ.ÇÁ.. |
00000010  08 05 d0 55 fe 04 2d d3 04 1f ca 8c 44 21 5b ff  |..ÐUþ.-Ó..Ê.D![ÿ|
00000020  60 c7 d7 03 16 be 40 1f 78 4a 3f 09 89 0b 9a 7d  |\`Ç×..¾@.xJ?....}|
00000030  4e c8 4e 6d 05 1e 01 8b 4c 24 00 00 00           |NÈNm....L$...|`)
.fromHexdump()
.gunzip();
message.toString() === "So long and thanks for all the fish." //true
```

## Import with ES6 `import` or CommonJS `require`

#### ES6 imports
You can import the default `chef` object:

```javascript
import chef from "CyberChef";
chef.toMorseCode("hello").toString();
// => .... . .-.. .-.. ---
```

Or you can import individual operationsm, benefitting from tree-shaking:
```javascript
import {toMorseCode} from "CyberChef";
toMorseCode("goodbye").toString();
// => --. --- --- -.. -... -.-- .
```

### CommonJS require

```javascript
const chef = require("CyberChef");
chef.toKebabCase("Large chicken shish, garlic mayo, no salad.").toString();
// => large-chicken-shish-garlic-mayo-no-salad
```

## Usage

> ### Operation names
> Operation names in the CyberChef API are camelCase versions of the operations on the web app, except for when the operation name begins with more than one uppercase character. For example, "Zlib Deflate" becomes `zlibDeflate`, but "SHA2" stays as `SHA2`.


### Operations

You can use operations with default config, or define your own arguments.

> To see what values an argument accepts, either refer to the UI or use `chef.help(<operation name>)` and inspect the args properties.

Operation with default args:
```javascript
chef.toCharcode("Service!") // Space delimiter, Base 16
// => 53 65 72 76 69 63 65 21
```

Configure args. Arg keys are camelCase versions of those displayed on the CyberChef UI.
```javascript
chef.toCharcode("Service!", {
    base: 8
    // delimiter stays as default
});
// => 123 145 162 166 151 143 145 41
```

Where arguments are options, they will need to be displayed exactly as on the UI. For example:
```javascript
// OK
chef.convertDistance(122, {
    inputUnits: "Kilometers (km)",
    outputUnits: "Cars (4m)"
});
// => 30500

// Invalid outputUnits value
chef.convertDistance(122, {
    inputUnits: "Kilometers (km)",
    outputUnits: "cars"
});
// => NaN
```

#### Operation input
Operations accept a wide range of input types. For most cases, you can throw any input type at an operation and it will deal with it. 

If the given input to an operation is not the operation's input type, it will attempt to convert the input before operating on it. For example, `toBase32`'s input type is `byteArray`, so if you input a string, it converts that string into a byte array before running the operation.

Files can be read into appropriate functions in chef. They are converted into ArrayBuffers.

JavaScript objects are accepted as input to operations that expect JSON, for example, `JSONBeautify`.

#### Operation arguments
Operation arguments are the second argument in an operation call. The best way to see an operation's arguments is to use `chef.help(<argument name here>)` or consult the UI.

Some operation arguments have default values. Some, for example `AESEncrypt`, will not work without specifying some argument.

**Number and String arguments**

// TODO

**option arguments**

// TODO

**toggleString arguments** (name????)

// TODO

**binaryString arguments** (any different??)

//TODO 

**boolean args** (same??)

// TODO


#### Operation return type
Operations return a `Dish` type. `Dish` has some functions and behaviours that make it easy to use. Most of the time you can ignore this and treat it as a string or a number.

Logging to the console, string coercion and number coercion work as you would expect:

```javascript
const result = toBase32(32).fromBase32();

// Logging to console
console.log(result); // => 32

// Number coercion
assert.equal(result + 3, 35); // => true

// String coercion
assert.equal(result.toString(), "32"); // => true;
```

The `value` property of an operation result will give the raw result.  For example:
```javascript
// dropBytes has output type ArrayBuffer
dropBytes("I'd love a byte").value
// => ArrayBuffer { byteLength: 8 }
```

[See more about `Dish` here.](#the-dish-type)

#### Compose operations
Operations can be composed. This example performs ROT13 substitution followed by conversion to Hex on some input:
```javascript
ROT13("Medium rare, please.").toHex().toString();
// => 5a 72 71 76 68 7a 20 65 6e 65 72 2c 20 63 79 72 6e 66 72 2e
```


#### Excluded operations
The best way to browse operations is to run Cyberchef, either locally or via the [demo][1].

Most CyberChef operations are inclided in the Node.js API, with the exception of operations that handle the Javascript `File` API and Flow Control operations. Excluded files will throw an ExcludedOperationError if called.

For a full list of excluded operations, see the source files under `src/node/config/excludedOperations`.


## Helper functions

The `chef` object has some other helpful functions.

#### `help`
The `help` function returns the description of an operation. Use this to see what input, output and arguments an operation has.

```javascript
chef.help(chef.toBase32)
/**
{ module: 'Default',
  description: 'Base32 is a notation for encoding arbitrary byte data using a restricted set of symbols that can be conveniently used by humans and processed by computers. It uses a smaller set of characters than Base64, usually the uppercase alphabet and the numbers 2 to 7.',
  inputType: 'byteArray',
  outputType: 'string',
  flowControl: false,
  args:
   [ { name: 'Alphabet',
       type: 'binaryString',
       value: 'A-Z2-7=',
       toggleValues: [] } ],
  name: 'To Base32' }
*/
```

`help` also accepts the name of an operation as a string, ignoring case and whitespace:
```javascript
chef.help("to base 32") // OK
chef.help("tobase32")   // OK
chef.help("toBase32")   // OK
```

#### `bake`
`bake` is useful for building "recipes" - chains of operations to apply to some input, in order. `bake` accepts operations in multiple ways. It always returns a `Dish` object.

One operation, no args
```javascript
chef.bake("I'll have the cod.", chef.toBase64);
// => SSdsbCBoYXZlIHRoZSBjb2Qu
```
One operation by name
```javascript
chef.bake("I'll have the cod.", "to base 64");
// => SSdsbCBoYXZlIHRoZSBjb2Qu
```
Multiple operations, by name or by function (default args)
```javascript
chef.bake("I'll have the cod", [chef.toBase64, "sha1"]);
// => aef8b5147a4b1a0dafb427bea903af0f3c8ea151
```

One operation, with custom args
```javascript
chef.bake("I'll have the salmon.", {
    op: chef.toBase64,
    args: {
        alphabet: "A-Z",
    },
});
// => SSCBYXZIHRZSBYW
```

Multiple operations with custom args
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

> ### Using recipes from CyberChef web tool
> `chef.bake` is compatible with the JSON format from saved recipes in the CyberChef web UI. Therefore, you can use the UI to figure out your recipe, and then export it to use in the Node.js API.


## The `Dish` type
All operations in CyberChef return the `Dish` type. This allows operations to be composed. You can also create a new `Dish` with some input and then perform operations on it.

Example:
```javascript
import { Dish } from "CyberChef";
const dish = new Dish("hello");
dish.toBase32().toString(); // => NBSWY3DP
```

For more information on `Dish`, see the [`Dish` API](#Dish) below

#### `Dish` coercion
Dish will coerce to a String or an Int where appropriate.

#### `Dish` examples

// Dish constructor

// Operaiton on dish that has byteArray value. Show use of toString

// use of number coercion

// operation chaining.

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

//TODO 

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
[ { module: 'Hashing',
    description: 'MD5 (Message-Digest 5) is a widely used hash function. It has been used in a variety of security applications and is also commonly used to check the integrity of files.<br><br>However, MD5 is not collision resistant and it isn\'t suitable for applications like SSL/TLS certificates or digital signatures that rely on this property.',
    infoURL: 'https://wikipedia.org/wiki/MD5',
    inputType: 'ArrayBuffer',
    outputType: 'string',
    flowControl: false,
    args: [],
    name: 'MD5' },
    ...
   ]

*/
```

### `chef.dish`
Class which is used to return operation results, or instantiate your own for operation composition

Also exposed as a top level export `Dish`.
#### Arguments:
|Name|Type|Description|
|-|-|-|
|`inputOrDish=null`|ArrayBuffer, String, Number, Object or Dish|The input on which you want to operate, or a Dish instance to clone a dish. If `null`, the dish's `value` and `type` properties are set to `[]` and `0` respectively.|
|`type=null`|`String`|The type that you want the input coerced to.|
#### Example:
```javascript
const dish = new chef.dish("hello");
dish.value; // "hello"
dish.type; // 1, signifying string type enum.


// Or as top level export
import { Dish } from "CyberChef"
const dish = new Dish("5", "number");
dish.value; // 5
dish.type; // 2;
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


[1]:https://gchq.github.io/CyberChef
[2]:https://github.com/gchq/CyberChef
[3]:https://developer.mozilla.org/en-US/docs/Web/API/File
[4]:https://github.com/gchq/CyberChef/issues
