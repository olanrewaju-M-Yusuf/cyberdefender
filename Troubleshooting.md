## Script error when running `cyberchef.htm` from a local file in Safari

Sometimes Safari throws a scriptError or syntaxError when you run `cyberchef.htm` from a file. The workaround for this is to run an instance of [`http-server`](https://www.npmjs.com/package/http-server) with compression enabled. You can do this with the following commands:

```
cd <path to directory containing cyberchef.htm>
npm install -g http-server
http-server -g .
```