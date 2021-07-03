
Tailwind
To v4 from v3
This guide only shows major changes that affect end users. For more details please see the changelog.

Node.js v4
If you are still using Node.js v4 or lower, you need to upgrade your Node.js installation to Node.js v6 or higher.

Instructions for upgrading your Node.js version can be found here.

CLI
The CLI has moved to a separate package: webpack-cli. You need to install it before using webpack, see basic setup.

The installation guide can be found here.

Update plugins
Many 3rd party plugins need to be updated to the latest versions to be compatible with webpack 4. Links to popular plugins can be found here.

mode
Add the new mode option to your configuration. Set it to 'production', 'development' or 'none' depending on your configuration type.

webpack.config.js

module.exports = {
  // ...
+  mode: 'production',
}
The purpose of 'development' mode and 'production' mode is different. You can use webpack-merge as shown in production guide to optimize configurations.

Deprecated/Removed plugins
These plugins can be removed from configuration as they are default in production mode:

webpack.config.js

module.exports = {
  // ...
  plugins: [
-    new NoEmitOnErrorsPlugin(),
-    new ModuleConcatenationPlugin(),
-    new DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("production") })
-    new UglifyJsPlugin()
  ],
}
These plugins are default in development mode

webpack.config.js

module.exports = {
  // ...
  plugins: [
-    new NamedModulesPlugin()
  ],
}
These plugins were deprecated and are now removed:

webpack.config.js

module.exports = {
  // ...
  plugins: [
-    new NoErrorsPlugin(),
-    new NewWatchingPlugin()
  ],
}
CommonsChunkPlugin
The CommonsChunkPlugin was removed. Instead the optimization.splitChunks options can be used.

See documentation of the optimization.splitChunks for more details. The default configuration may already suit your needs.

When generating the HTML from the stats you can use optimization.splitChunks.chunks: "all" which is the optimal configuration in most cases.

import() and CommonJS
When using import() to load non-ESM the result has changed in webpack 4. Now you need to access the default property to get the value of module.exports.

non-esm.js

module.exports = {
  sayHello: () => {
    console.log('hello world');
  }
};
example.js

function sayHello() {
  import('./non-esm.js').then(module => {
    module.default.sayHello();
  });
}
json and loaders
When using a custom loader to transform .json files you now need to change the module type:

webpack.config.js

module.exports = {
  // ...
  rules: [
    {
      test: /config\.json$/,
      loader: 'special-loader',
+     type: 'javascript/auto',
      options: {...}
    }
  ]
};
When still using the json-loader, it can be removed:

webpack.config.js

module.exports = {
  // ...
  rules: [
    {
-     test: /\.json$/,
-     loader: 'json-loader'
    }
  ]
};
module.loaders
module.loaders were deprecated since webpack 2 and are now removed in favor of module.rules.

Recordar: cuando uso babel-loader agregar jsx y resolve: {
          extensions: ['.js', '.jsx'],
        },
        
cuando uso HMR tener el .env con production/development (para .babelrc) y el webpack.config.js mode: production.

Al usar minicssextractplugin ver en webpack para ssr:
Using preloaded or inlined CSS
The runtime code detects already added CSS via <link> or <style> tag. This can be useful when injecting CSS on server-side for Server-Side-Rendering. The href of the <link> tag has to match the URL that will be used for loading the CSS chunk. The data-href attribute can be used for <link> and <style> too. When inlining CSS data-href must be used.

Loadable components forward ref error.

Loadable components async error

Loadable components node and web env shares same webpack config file. 

SSR and semantic ui 

Change from semantic ui to tailwind

Investigar stylelint en https://styled-components.com/docs/tooling#babel-plugin

Context api in ssr

Test on heroku

Redux persister working
