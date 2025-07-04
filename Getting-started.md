## Installing

CyberChef uses the Grunt build system, so it's very easy to install. You'll need to carry out the following steps to get going:

 1. Install Git
 2. Install Node.js version 18 and npm version 8 ([nvm](https://github.com/nvm-sh/nvm) is usually a good way of managing your Node versions)
 3. `git clone https://github.com/gchq/CyberChef.git`
 4. `cd CyberChef`
 5. `npm install`

npm will then install all the dependencies needed to build and run CyberChef.

> Consider adding `export NODE_OPTIONS=--max_old_space_size=2048` to your `~/.bashrc` file. If you attempt to build a production version of CyberChef, you may get a "JavaScript heap out of memory" error if you do not set this environment variable. You can also run `npm run setheapsize` to do this but it will not persist between terminal sessions.

## Compiling

> Note that CyberChef only supports bash-based dev environments, so primarily Linux and Mac. You may be able to get a build working on Windows, but it is not officially supported.

npm has been configured with several scripts to aid in the development process:


```
npm start
```
> Use this when developing new functionality. It will launch a web server on port 8080 hosting an uncompressed, development version of CyberChef, accessible by browsing to [`localhost:8080`](http://localhost:8080). Whenever a source file is modified, the development version will be rebuilt automatically.


```
npm run build
```
> When you are ready to create a production build, run this command. It will lint, test, compile and compress all the source files and create a production-ready build in `build/prod/`.


```
npm run node
```
> This will package up CyberChef as a NodeJS library. More info on this can be found here: https://github.com/gchq/CyberChef/wiki/Node-API

```
npm test
```
> This will run all the pre-configured tests and output the results to stdout.


## Repository structure

 - `build/`
     - `prod/` - This folder contains the most recently built production version of CyberChef including the inline version. It is populated by running `grunt prod`.
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