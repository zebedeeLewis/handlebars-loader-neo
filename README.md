# handlebars-loader-neo

Produce the results of compiling the given handlebars template.

## Getting Started

First, install `handlebars-loader-neo`:

```console
npm install --save-dev handlebars-loader-neo
```

Create your template files:

**header.hbs**
```html
<html>
  <head>
    <title>My Page</title>
    ...
  </head>
  <body>
    <nav class="main-nav">
      ...
    </nav>
    <header>
      <img alt="..." src="./banner.jpg">
    </header>
```

**footer.hbs**
```html
    <footer>
      ...
    </footer>
  <body>
</html>
```

**index.hbs**
```html
{{> ./header }}
<main>
  ...
</main>
{{> ./footer }}
```

Import your main template:

**file.hbs**

```js
import html from './index.hbs'
```

Then add the loader to your `webpack` config:

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.hbs$/i,
        loader: 'handlebars-loader-neo',
      },
    ],
  },
};
```

Use `html-loader` to include images, videos etc. found in the templates:

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.hbs$/i,
        use: [
          {
            loader: "handlebars-loader-neo",
            options: { inputCompatibility: "htmlLoader" },
          },
          { loader: "html-loader" },
        ],
      },
    ],
  },
};

```

## Options

|           Name                                |        Type                                                |        Default                        |
| :-------------------------                    | :-----------------:                                        | :-------------------------------:     |
| **[`extensions`](#extensions)**               | `{Array.<string>}`                                         | `['.handlebars', 'hbs', '.hb']`       |
| **[`rootPath`](#rootPath)**                   | `{string}`                                                 | `The current request context or './'` |
| **[`rootRelative`](#rootRelative)**           | `{string}`                                                 | `The current request context or './'` |
| **[`inlineRequires`](#inlineRequires)**       | `{(null\|string\|RegExp\|Array.<(null\|string\|RegExp)>)}` | `null`                                |
| **[`logLevel`](#logLevel)**                   | `{(0\|1\|2\|3)}`                                           | `0`                                   |
| **[`debug`](#debug)**                         | `{(0\|1\|2\|3)}`                                           | `0`                                   |
| **[`runtimePath`](#runtimePath)**             | `{string}`                                                 | `'handlebars/runtime'`                |
| **[`runtime`](#runtime)**                     | `{string}`                                                 | `'handlebars/runtime'`                |
| **[`helpersDirs`](#helpersDirs)**             | `{Array.<string>}`                                         | `[]`                                  |
| **[`partialDirs`](#partialDirs)**             | `{Array.<string>}`                                         | `[]`                                  |
| **[`ignoreHelpers`](#ignoreHelpers)**         | `{(null\|string\|RegExp\|Array.<(null\|string\|RegExp)>)}` | `null`                                |
| **[`ignorePartials`](#ignorePartials)**       | `{(null\|string\|RegExp\|Array.<(null\|string\|RegExp)>)}` | `null`                                |
| **[`parseOptions`](#parseOptions)**           | `{Handlebars.ParseOptions}`                                | `{}`                                  |
| **[`precompileOptions`](#precompileOptions)** | `{Handlebars.PrecompileOptions}`                           | `{}`                                  |
| **[`failLoudly`](#failLoudly)**               | `{boolean}`                                                | `true`                                |
| **[`templateContextFile`](#templateContextFile)** |`{string}`                                              | `''`                                  |
| **[`inputCompatibility`](#inputCompatibility)** | `{(default\|htmlLoader)}`                                | `'default'`                           |
| **[`outputFormat`](#outputFormat)**           | `{(default)}`                                              | `'defulat'`                           |

### `extensions`
Type: `{Array.<string>}`
Default: `['.handlebars', 'hbs', '.hb']`

The file extensions to try when resolving resource names. This array is always merged with a default set of extensions. Order matters, the extensions will be tried from lowest index to highest, with the defaults being tried last.


### `rootPath`
Type: `{string}`
Default: `The current request context or './'`

Used as the root path for resolving "root relative" resource names. Root relative resource names are names that are not absolute paths and do not start with a "dot directory". If both this option and "rootRelative" are set, this value should take presidence.


### `rootRelative`
Type: `{string}`
Default: `The current request context or './'`

Alias for `rootPath`. For compatibility with `handlebars-loader`


### `inlineRequires`
Type: `{(null|string|RegExp|Array.<(null|string|RegExp)>)}`
Default: `null`

Zero or more regular expressions each matching a string to be replaced with a require statement. Each RegExp must contain a named group named "path" that captures the string that should be used as the require statements path.

> ⚠ Be careful of greedy regular expressions!

example:

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.hbs$/i,
        use: [
          {
            loader: "handlebars-loader-neo",
            options: { inlineRequires: /<audio\s+.*?src="(?<path>.*?)"\s+.*?>/gi },
          },
        ],
      },
    ],
  },
};

```

 Inserting the string `....` into your array of patterns will cause a default set of patterns to be appended to your array of patterns. The defaults currently include img tag src attribute, and source tag srcset attribute:

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.hbs$/i,
        use: [
          {
            loader: "handlebars-loader-neo",
            options: {
              inlineRequires: [ 
                '....'
                /<audio\s+.*?src="(?<path>.*?)"\s+.*?>/gi,
              ]
            },
          },
        ],
      },
    ],
  },
};

```


### `logLevel`
Type: `{(0|1|2|3)}`
Default: `0`

Toggles logging information. To stay compatible with the original handlebars-loader, setting this value to "true" set the log level to LogLevel.Debug. Setting this value to "false" does nothing. The log level remains as is. Log level 0 is turns logging off, 1 sets logging to "Error", 2 sets logging to "Warning", and 3 sets logging to "Info".


### `debug`
Type: `{(0|1|2|3)}`
Default: `0`

Alias for `logLevel`.


### `runtimePath`
Type: `{string}`
Default: ``'handlebars/runtime'``

Alias for `runtimePath`.

Path to the runtime library.


### `runtime`
Type: `{string}`
Default: ``'handlebars/runtime'``


### `helpersDirs`
Type: `{Array.<string>}`
Default: `[]`

Defines additional directories to be searched for helpers. Allows helpers to be defined in a directory and used globally without relative paths. You must surround helpers in subdirectories with brackets (Handlerbar helper identifiers can't have forward slashes without this).


### `partialDirs`
Type: `{Array.<string>}`
Default: `[]`

Defines additional directories to be searched for partials. Allows partials to be defined in a directory and used globally without relative paths.


### `ignoreHelpers`
Type: `{(null|string|RegExp|Array.<(null|string|RegExp)>)}`
Default: `null`

Prevents matching helpers from being loade at build time. Useful for manually loading partials at runtime.


### `ignorePartials`
Type: `{(null|string|RegExp|Array.<(null|string|RegExp)>)}`
Default: `null`

Prevents matching partials from being loaded at build time. Useful for manually loading partials at runtime.


### `parseOptions`
Type: `{Handlebars.ParseOptions}`
Default: `{}`

Options to pass to the Handlebars parser.


### `precompileOptions`
Type: `{Handlebars.PrecompileOptions}`
Default: `{}`

Options to pass to the Handlebars precompiler.


### `failLoudly`
Type: `{boolean}`
Default: `true`

If "true", the loader will throw a fatal error whenever a problem is encountered, stopping the compilation. If "false", the compilation will simply continue. If one or more errors prevent the compilation from being successfull, it will simply produce an empty string.


### `templateContextFile`
Type: `{string}`
Default: `''`

The path to a JS file that exports a simple Object to be used as the context when compiling the template.


### `inputCompatibility`
Type: `{(default|htmlLoader)}`
Default: `'default'`

Prepares the loader to accept input from different kinds of loader.


### `outputFormat`
Type: `{(default)}`
Default: `'default'`

Format the loader to be passed to another loader.


## License
[MIT](https://choosealicense.com/licenses/mit/)p
