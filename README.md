# Webpack Demo

<https://webpack.js.org/guides/getting-started/>

Webpack is used to compile JavaScript modules.

Webpack is a static module bundler for modern JavaScript applications. When webpack processes your application, it internally builds a dependency graph from one or more entry points and then combines every module your project needs into one or more bundles, which are static assets to serve your content from.

Create a directory, initialize npm, install webpack locally, and install the webpack-cli.

```bash
mkdir webpack-demo
cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```

Create the following directory structure, files and their contents

```bash
webpack-demo
|- package.json
|- package-lock.json
|- index.html
|- /src
  |- index.js
```

Adjust package.json file in order to make sure we mark our package as private, as well as removing the main entry. This is to prevent an accidental publish of your code.

<https://docs.npmjs.com/cli/v10/configuring-npm/package-json>

## Creating a Bundle

Separate the "source" code (./src) from our "distribution" code (./dist). The "source" code is the code that we'll write and edit. The "distribution" code is the minimized and optimized output of our build process that will eventually be loaded in the browser. Tweak the directory structure:

```bash
webpack-demo
|- package.json
|- package-lock.json
|- /dist
  |- index.html
|- /src
  |- index.js
```

Later on in another guide, we will generate index.html rather than edit it manually:

<https://webpack.js.org/guides/output-management/#setting-up-htmlwebpackplugin>

To bundle the lodash dependency with index.js, we'll need to install the library locally:

`npm install --save lodash`

When installing a package that will be bundled into your production bundle, you should use `npm install --save`. If you're installing a package for development purposes (e.g. a linter, testing libraries, etc.) then you should use `npm install --save-dev`.

Import lodash in src/index.js

Now, since we'll be bundling our scripts, we have to update our `index.html` file. Let's remove the lodash `<script>`, as we now import it, and modify the other `<script>` tag to load the bundle, instead of the raw `./src` file:

## dist/index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Getting Started</title>
  </head>
  <body>
  <script src="main.js"></script>
  </body>
</html>
```

In this setup, `index.js` explicitly requires lodash to be present, and binds it as `_` (no global scope pollution). By stating what dependencies a module needs, webpack can use this information to build a dependency graph. It then uses the graph to generate an optimized bundle where scripts will be executed in the correct order.

Run npx webpack, which will take our script at `src/index.js` as the entry point, and will generate `dist/main.js` as the output. The npx command, which ships with Node 8.2/npm 5.2.0 or higher, runs the webpack binary (./node_modules/.bin/webpack) of the webpack package we installed in the beginning:

`npx webpack`

# Modules

The import and export statements have been standardized in ES2015. They are supported in most of the browsers at this moment, however there are some browsers that don't recognize the new syntax. But don't worry, webpack does support them out of the box.

Behind the scenes, webpack "transpiles" the code so that older browsers can also run it. Webpack supports various other module syntaxes as well, see Module API for more information.

## Babel

Note that webpack will not alter any code other than import and export statements. If you are using other ES2015 features, make sure to use a transpiler such as Babel via webpack's loader system.

## Using a Configuration

As of version 4, webpack doesn't require any configuration, but most projects will need a more complex setup. This is much more efficient than having to manually type in a lot of commands in the terminal, so let's create one

Now, let's run the build again but instead using our new configuration file:

`$ npx webpack --config webpack.config.js`

If a webpack.config.js is present, the webpack command picks it up by default. We use the `--config` option here only to show that you can pass a configuration of any name. This will be useful for more complex configurations that need to be split into multiple files.

A configuration file allows far more flexibility than CLI usage. We can specify loader rules, plugins, resolve options and many other enhancements this way

<https://webpack.js.org/configuration>

## NPM Scripts

<https://docs.npmjs.com/cli/v10/using-npm/scripts>

The "scripts" property of your package.json file supports a number of built-in scripts and their preset life cycle events as well as arbitrary scripts. These all can be executed by running `npm run-script <stage>` or `npm run <stage>` for short.

Given it's not particularly fun to run a local copy of webpack from the CLI, we can set up a little shortcut. Let's adjust our package.json by adding an npm script:

Now the `npm run build` command can be used in place of the npx command we used earlier. Note that within scripts we can reference locally installed npm packages by name the same way we did with npx. This convention is the standard in most npm-based projects because it allows all contributors to use the same set of common scripts.

Custom parameters can be passed to webpack by adding two dashes between the npm run build command and your parameters

## Asset Management

Prior to webpack, front-end developers would use tools like grunt and gulp to process these assets and move them from their /src folder into their /dist or /build directory. The same idea was used for JavaScript modules, but tools like webpack will dynamically bundle all dependencies into a dependency graph. Now every module now explicitly states its dependencies and we'll avoid bundling modules that aren't in use.

You can also include any other type of file, besides JavaScript, for which there is a loader or built-in Asset Modules support. This means that the same benefits listed above for JavaScript (e.g. explicit dependencies) can be applied to everything used in building a website or web app. Let's start with CSS.

## Loading CSS

In order to import a CSS file from within a JavaScript module, you need to install and add the `style-loader` and `css-loader` to your module configuration:

`npm install --save-dev style-loader css-loader`

webpack.config.js

```js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
module: {
  rules: [
    {
      test: /\.css$/i,
      use: ['style-loader', 'css-loader'],
    },
  ],
},
};
```

Module loaders can be chained. Each loader in the chain applies transformations to the processed resource. A chain is executed in reverse order. The first loader passes its result (resource with applied transformations) to the next one, and so forth. Finally, webpack expects JavaScript to be returned by the last loader in the chain.

The above order of loaders should be maintained: 'style-loader' comes first and followed by 'css-loader'. If this convention is not followed, webpack is likely to throw errors.

Tip
webpack uses a regular expression to determine which files it should look for and serve to a specific loader. In this case, any file that ends with .css will be served to the style-loader and the css-loader.

This enables you to import `'./style.css'` into the file that depends on that styling. Now, when that module is run, a `<style>` tag with the stringified css will be inserted into the `<head>` of your html file.

Let's try it out by adding a new style.css file to our project and import it in our index.js.

## minimize

You can, and in most cases should, minimize css for better load times in production. On top of that, loaders exist for pretty much any flavor of CSS you can think of – postcss, sass, and less to name a few.

<https://webpack.js.org/plugins/mini-css-extract-plugin/#minimizing-for-production>

## Loading Images

As of webpack 5, using the built-in Asset Modules we can easily incorporate those in our system as well:

webpack.config.js

```js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ['style-loader', 'css-loader'],
      },
    {
      test: /\.(png|svg|jpg|jpeg|gif)$/i,
      type: 'asset/resource',
    },
    ],
  },
};
```

Now, when you import `MyImage` from `'./my-image.png'`, that image will be processed and added to your output directory and the `MyImage` variable will contain the final url of that image after processing. 

When using the `css-loader`, as shown above, a similar process will occur for url('./my-image.png') within your CSS. The loader will recognize this is a local file, and replace the `'./my-image.png'` path with the final path to the image in your output directory. The html-loader handles `<img src="./my-image.png" />` in the same manner.

Let's add an `icon.png` to `/src`

Let's create a new build and open up the index.html file again:

`$ npm run build`

If all went well, you should now see your icon as a repeating background, as well as an img element beside our Hello webpack text. If you inspect this element, you'll see that the actual filename has changed to something like 29822eaa871e8eadeaa4.png. This means webpack found our file in the src folder and processed it!

## Output Management

First, let's adjust our project a little bit:

project

```bash
webpack-demo
|- package.json
|- package-lock.json
|- webpack.config.js
|- /dist
|- /src
  |- index.js
  |- print.js
|- /node_modules
```

src/print.js

```js
export default function printMe() {
  console.log('I get called from print.js!');
}
```

And use that function in our src/index.js file:

src/index.js

```js
import _ from 'lodash';
import printMe from './print.js';

 function component() {
  const element = document.createElement('div');
  const btn = document.createElement('button');

  element.innerHTML = _.join(['Hello', 'webpack'], ' ');

  btn.innerHTML = 'Click me and check the console!';
  btn.onclick = printMe;

  element.appendChild(btn);

  return element;
 }

document.body.appendChild(component());
```

Let's also update our dist/index.html file, in preparation for webpack to split out entries:

dist/index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
  <title>Output Management</title>
  <script src="./print.bundle.js"></script>
  </head>
  <body>
  <script src="./index.bundle.js"></script>
  </body>
</html>
```

Now adjust the config. We'll be adding our src/print.js as a new entry point (print) and we'll change the output as well, so that it will dynamically generate bundle names, based on the entry point names:

webpack.config.js

```js
const path = require('path');

module.exports = {
entry: {
  index: './src/index.js',
  print: './src/print.js',
},
  output: {
  filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

Run `npm run build` and see what this generates:

## Setting up HtmlWebpackPlugin

First install the plugin and adjust the webpack.config.js file:

`npm install --save-dev html-webpack-plugin`

webpack.config.js
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: {
    index: './src/index.js',
    print: './src/print.js',
  },
plugins: [
  new HtmlWebpackPlugin({
    title: 'Output Management',
  }),
],
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

Before we do a build, you should know that the HtmlWebpackPlugin by default will generate its own index.html file, even though we already have one in the dist/ folder. This means that it will replace our index.html file with a newly generated one. Let's see what happens when we do an npm run build:

If you open index.html in your code editor, you'll see that the HtmlWebpackPlugin has created an entirely new file for you and that all the bundles are automatically added.

If you want to learn more about all the features and options that the HtmlWebpackPlugin provides, then you should read up on it on the HtmlWebpackPlugin repo. <https://github.com/jantimon/html-webpack-plugin>

