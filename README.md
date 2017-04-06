# api documentation for  eyes (v0.1.8)  [![npm package](https://img.shields.io/npm/v/npmdoc-eyes.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-eyes) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-eyes.svg)](https://travis-ci.org/npmdoc/node-npmdoc-eyes)
#### a customizable value inspector

[![NPM](https://nodei.co/npm/eyes.png?downloads=true)](https://www.npmjs.com/package/eyes)

[![apidoc](https://npmdoc.github.io/node-npmdoc-eyes/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-eyes_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-eyes/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-eyes/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-eyes/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Alexis Sellier",
        "email": "self@cloudhead.net"
    },
    "contributors": [
        {
            "name": "Charlie Robbins",
            "email": "charlie@nodejitsu.com"
        }
    ],
    "dependencies": {},
    "description": "a customizable value inspector",
    "devDependencies": {},
    "directories": {
        "lib": "./lib",
        "test": "./test"
    },
    "dist": {
        "shasum": "62cf120234c683785d902348a800ef3e0cc20bc0",
        "tarball": "https://registry.npmjs.org/eyes/-/eyes-0.1.8.tgz"
    },
    "engines": {
        "node": "> 0.1.90"
    },
    "keywords": [
        "inspector",
        "debug",
        "inspect",
        "print"
    ],
    "licenses": [
        "MIT"
    ],
    "main": "./lib/eyes",
    "maintainers": [
        {
            "name": "cloudhead",
            "email": "self@cloudhead.net"
        },
        {
            "name": "indexzero",
            "email": "charlie.robbins@gmail.com"
        }
    ],
    "name": "eyes",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "scripts": {
        "test": "node test/*-test.js"
    },
    "url": "http://github.com/cloudhead/eyes.js",
    "version": "0.1.8"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module eyes](#apidoc.module.eyes)
1.  [function <span class="apidocSignatureSpan">eyes.</span>inspect (obj, label, options)](#apidoc.element.eyes.inspect)
1.  [function <span class="apidocSignatureSpan">eyes.</span>inspector (options)](#apidoc.element.eyes.inspector)
1.  [function <span class="apidocSignatureSpan">eyes.</span>print (str, label, options)](#apidoc.element.eyes.print)
1.  [function <span class="apidocSignatureSpan">eyes.</span>stylize (str, style, styles)](#apidoc.element.eyes.stylize)
1.  object <span class="apidocSignatureSpan">eyes.</span>defaults



# <a name="apidoc.module.eyes"></a>[module eyes](#apidoc.module.eyes)

#### <a name="apidoc.element.eyes.inspect"></a>[function <span class="apidocSignatureSpan">eyes.</span>inspect (obj, label, options)](#apidoc.element.eyes.inspect)
- description and source-code
```javascript
inspect = function (obj, label, options) {
    options = merge(this.defaults, options || {});

    if (options.stream) {
        return this.print(stringify(obj, options), label, options);
    } else {
        return stringify(obj, options) + (options.styles ? '\033[39m' : '');
    }
}
```
- example usage
```shell
...

a customizable value inspector for Node.js

synopsis
--------

I was tired of looking at cluttered output in the console -- something needed to be done,
'sys.inspect()' didn't display regexps correctly, and was too verbose, and I had an hour or two to spare.
So I decided to have some fun. _eyes_ were born.

![eyes-ss](http://dl.dropbox.com/u/251849/eyes-js-ss.gif)

_example of the output of a user-customized eyes.js inspector_

*eyes* also deals with circular objects in an intelligent way, and can pretty-print object literals.
...
```

#### <a name="apidoc.element.eyes.inspector"></a>[function <span class="apidocSignatureSpan">eyes.</span>inspector (options)](#apidoc.element.eyes.inspector)
- description and source-code
```javascript
inspector = function (options) {
    var that = this;
    return function (obj, label, opts) {
        return that.inspect.call(that, obj, label,
            merge(options || {}, opts || {}));
    };
}
```
- example usage
```shell
...
_example of the output of a user-customized eyes.js inspector_

*eyes* also deals with circular objects in an intelligent way, and can pretty-print object literals.

usage
-----

var inspect = require('eyes').inspector({styles: {all: 'magenta'}});

inspect(something); // inspect with the settings passed to 'inspector'

or

var eyes = require('eyes');
...
```

#### <a name="apidoc.element.eyes.print"></a>[function <span class="apidocSignatureSpan">eyes.</span>print (str, label, options)](#apidoc.element.eyes.print)
- description and source-code
```javascript
print = function (str, label, options) {
    for (var c = 0, i = 0; i < str.length; i++) {
        if (str.charAt(i) === '\033') { i += 4 } // '4' because '\033[25m'.length + 1 == 5
        else if (c === options.maxLength) {
           str = str.slice(0, i - 1) + '…';
           break;
        } else { c++ }
    }
    return options.stream.write.call(options.stream, (label ?
        this.stylize(label, options.styles.label, options.styles) + ': ' : '') +
        this.stylize(str,   options.styles.all, options.styles) + '\033[0m' + "\n");
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.eyes.stylize"></a>[function <span class="apidocSignatureSpan">eyes.</span>stylize (str, style, styles)](#apidoc.element.eyes.stylize)
- description and source-code
```javascript
stylize = function (str, style, styles) {
    var codes = {
        'bold'      : [1,  22],
        'underline' : [4,  24],
        'inverse'   : [7,  27],
        'cyan'      : [36, 39],
        'magenta'   : [35, 39],
        'blue'      : [34, 39],
        'yellow'    : [33, 39],
        'green'     : [32, 39],
        'red'       : [31, 39],
        'grey'      : [90, 39]
    }, endCode;

    if (style && codes[style]) {
        endCode = (codes[style][1] === 39 && styles.all) ? codes[styles.all][0]
                                                         : codes[style][1];
        return '\033[' + codes[style][0] + 'm' + str +
               '\033[' + endCode + 'm';
    } else { return str }
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
