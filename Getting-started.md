## Installing

CyberChef uses the Grunt build system, so it's very easy to install. You'll need to carry out the following steps to get going:

 1. Install Git
 2. Install Node.js and its package manager, npm
 3. Install Grunt: `npm install -g grunt-cli`
 4. `git clone https://github.com/gchq/CyberChef.git`
 5. `cd CyberChef`
 6. `npm install`

npm will then install all the dependencies needed by Grunt.


## Compiling

Grunt has been configured with several tasks to aid in the development process:


```
grunt dev
```
> Use this when developing new functionality. It will launch a web server on port 8080 hosting an uncompressed, development version of CyberChef, accessible by browsing to [`localhost:8080`](http://localhost:8080). Whenever a source file is modified, the development version will be rebuilt automatically.

> Note: This task will initially result in an error relating to the `MetaConfig.js` file but will quickly rebuild and should complete successfully. This is due to the `MetaConfig.js` file being built at the same time as the rest of the app and therefore not being available for compilation immediately.


```
grunt prod
```
> When you are ready to create a production build, run this command. It will lint, test, compile and compress all the source files and create a production-ready build in `build/prod`. It will also create the inline version of CyberChef at the same location, called `cyberchef.htm`.


```
grunt node
```
> This will package up CyberChef as a NodeJS library, creating the file `build/node/CyberChef.js`. It can then be loaded and used like so:
> ```javascript
> > var chef = require("./CyberChef.js")
> undefined
> > chef.bake("test", [{"op":"To Hex","args":["Space"]}]).then(r => { console.log(r) })
> Promise {
> ... }
> > { result: '74 65 73 74',
>   type: 'string',
>   progress: 1,
>   duration: 2,
>   error: false }
> ```


```
grunt test
```
> This will run all the pre-configured tests and output the results to stdout.


```
grunt docs
```    
> Builds the codebase documentation and places it in the `docs` directory.


## Repository structure

 - `build/`
     - `dev/` - This will be populated with an uncompressed development build of CyberChef by running the `grunt dev` command.
     - `prod/` - This folder contains the most recently built production version of CyberChef including the inline version. It is populated by running `grunt prod`.
     - `node/` - Populated with the packaged NodeJS version of CyberChef by running the `grunt node` command.
 - `src/`
     - `core/` - Core CyberChef files that make up the heart of the application
         - `config/` - Files specifying the operation configurations
             - `modules/` - Automatically generated module references
         - `lib/` - Libraries containing shared code for multiple operations
         - `errors/` - Custom error types
         - `operations/` - Operation objects
         - `vendor/` - Libraries that cannot currently be imported through npm
     - `node/` - Wrappers for the NodeJS version of CyberChef
     - `web/` - The code which makes up the CyberChef web app
         - `css/`
             - `lib/` - Library CSS and Less files
             - `structure/` - Structural styles to lay out the stage
             - `themes/` - Look and feel styles
         - `html/`
             - `index.html` - The CyberChef page structure
         - `static/` - Static files like images
 - `docs/` - Codebase documentation, populated by running `grunt docs`.
 - `test/`
     - `tests/` - Configuration for tests on operations and recipes
 - `.babelrc` - Babel transpilation configuration
 - `.editorconfig` - Text editor conventions stored in a cross-compatible format
 - `.travis.yml` - Travis CI build process configuration
 - `Gruntfile.js` - Grunt build process configuration
 - `webpack.config.js` - Webpack configuration
 - `postcss.config.js` - PostCSS configuration
 - `LICENSE` - The Apache 2.0 licence information
 - `package.json` - npm configuration and a list of all the dependencies
 - `README.md` - An introduction to CyberChef