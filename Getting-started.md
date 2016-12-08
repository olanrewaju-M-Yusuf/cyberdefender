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
Use this when developing new functionality. It will launch a persistent task which will automatically build an uncompressed, development version of CyberChef located in `build/dev`. Whenever a source file is modified, the development version will be rebuilt.

```
grunt prod
```
When you are ready to create a production build, run this command. It will lint, concatenate, and compress all the source files and create a production-ready build in `build/prod`. It will also create the inline version of CyberChef at the same location.

```
grunt stats
```
This command will give you statistics about the code base such as how many lines there are as well as details of file sizes before and after compression.

```
grunt docs
```    
This will build the codebase documentation and place it in the `docs` directory.


## Repository structure

 - `build/`
     - `dev/` - This will be populated with an uncompressed development build of CyberChef by running the `grunt dev` command.
     - `prod/` - This folder contains the most recently built production version of CyberChef including the inline version. It is populated by running `grunt prod`.
 - `src/`
     - `css/`
         - `lib/` - Various library CSS files
         - `structure/` - Structural styles to lay out the stage
         - `themes/` - Look and feel styles
     - `html/`
         - `index.html` - The CyberChef page structure
     - `js/`
         - `config/` - Files specifying the operation configurations
         - `core/` - Core CyberChef files that make up the heart of the application
         - `lib/` - Libraries... oh so many libraries...
         - `operations/` - Operation objects
         - `views/` - Code to handle the various CyberChef views
             - `html/` - The code which makes up the CyberChef web app
     - `static/` - Static files like images
 - `docs/` - Codebase documentation, populated by running `grunt docs`.
 - `Gruntfile.js` - Grunt build process configuration
 - `LICENSE` - The Apache 2.0 licence information
 - `package.json` - npm configuration and a list of all the dependencies
 - `README.md` - An introduction to CyberChef
