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

When installing a package that will be bundled into your production bundle, you should use npm install --save. If you're installing a package for development purposes (e.g. a linter, testing libraries, etc.) then you should use npm install --save-dev.

Now, let's import lodash in our script

