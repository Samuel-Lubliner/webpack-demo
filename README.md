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

