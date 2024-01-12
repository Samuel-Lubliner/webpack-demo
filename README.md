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