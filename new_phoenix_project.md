# New Phoenix Project Setup with TailwindCSS and PostCSS

These install instructions guide you through modifying the default Phoenix project to 

* add PostCSS
* add TailwindCSS version 2
* optionally switch from SASS to CSS
* optionally move to Webpack 5

Note: I have been unsuccessful with using Node version 16. You can switch to a previous version (14 or 15) using NVM.
### Ensure Latest Version of Phoenix

You may need to update your version of Phoenix. You can find the latest version of latest Pheonix at [Phoenix Installation](https://hexdocs.pm/phoenix/installation.html). (*These install steps were last updated for* **Phoenix 1.5.8**)

```bash
mix local.hex --force
mix archive.install hex phx_new --force
```

### Create Your Project

```bash
mix phx.new --live my_app
cd my_app
mix ecto.create
```

### Install PostCSS, TailWindCSS, and Dependencies
This installs TailwindCSS 2 as a PostCSS plugin. Several first-party plugins are included here if you need them. It is OK to leave them in since they are purged from the production release if not used. 

The next-to-last command installs the Tailwind config and the last command installs the full version of the Tailwind config file that I use as a reference when needed.

#### If using Webpack 4...

(the current default in Phoenix 1.5.8) use the following to install TailWindCSS:

```bash
cd assets

npm install \
  postcss-nested@5.0.5 postcss@8.1.13 \
  postcss-loader@4.2 postcss-import@12.0.1 \
  tailwindcss \
  @tailwindcss/forms \
  @tailwindcss/typography \
  @tailwindcss/aspect-ratio \
  autoprefixer@10.0.2 --save-dev

npx tailwindcss init
npx tailwind init tailwindcss-full.js --full
```

(end of Webpack 4 specific instructions)

#### If using Webpack 5...

Replace `devDependencies` in `assets/package.json` with:

```javascript
  "devDependencies": {
    "@babel/preset-env": "^7.14.1",
    "autoprefixer": "^10.2.5",
    "babel-loader": "^8.2.2",
    "copy-webpack-plugin": "^8.1.1",
    "css-loader": "^5.2.4",
    "mini-css-extract-plugin": "^1.6.0",
    "postcss": "^8.2.14",
    "postcss-import": "^14.0.1",
    "postcss-loader": "^5.2.0",
    "webpack": "^5.36.2",
    "webpack-cli": "^4.7.0"
  }
```

Edit `webpack.config.js` to remove `HardSourceWebpackPlugin` and `OptimizeCSSAssetsPlugin`. Remember to delete both the `require` line and the instantiation line.

```javascript
  // const HardSourceWebpackPlugin = require('hard-source-webpack-plugin');
...
  // const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
...
  // new OptimizeCSSAssetsPlugin({})
...
  // .concat(devMode ? [new HardSourceWebpackPlugin()] : [])

```

And update the constructors for `TerserPlugin` and `CopyWebpackPlugin`
```javascript
        new TerserPlugin({ parallel: true }),
...
        new CopyWebpackPlugin({
          patterns: [
            { from: "static/", to: "../" },
          ]
        })
```

Here's the full version of `webpack.config.js`
```javascript
const path = require('path');
const glob = require('glob');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const TerserPlugin = require('terser-webpack-plugin');
const CopyWebpackPlugin = require('copy-webpack-plugin');

module.exports = (env, options) => {
  const devMode = options.mode !== 'production';

  return {
    optimization: {
      minimizer: [
        new TerserPlugin({ parallel: true }),
      ]
    },
    entry: {
      'app': glob.sync('./vendor/**/*.js').concat(['./js/app.js'])
    },
    output: {
      filename: '[name].js',
      path: path.resolve(__dirname, '../priv/static/js'),
      publicPath: '/js/'
    },
    devtool: devMode ? 'eval-cheap-module-source-map' : undefined,
    module: {
      rules: [{
          test: /\.js$/,
          exclude: /node_modules/,
          use: {
            loader: 'babel-loader'
          }
        },
        {
          test: /\.[s]?css$/,
          use: [
            MiniCssExtractPlugin.loader,
            'css-loader',
            'postcss-loader',
          ],
        }
      ]
    },
    plugins: [
      new MiniCssExtractPlugin({ filename: '../css/app.css' }),
      new CopyWebpackPlugin({
        patterns: [
          { from: "static/", to: "../" },
        ]
      })
    ]
  }
};
```

**Now**, run the following to install TailwindCSS:

```bash
cd assets

npm install \
  tailwindcss \
  @tailwindcss/forms \
  @tailwindcss/typography \
  @tailwindcss/aspect-ratio \
  --save-dev

npx tailwindcss init
npx tailwind init tailwindcss-full.js --full
```

(end of Webpack 5 specific instructions)

### Customize TailWindCSS config
Configure `purge` and `plugins` in `tailwind.config.js`

```javascript
// tailwind.config.js
  purge: [
    "../lib/**/*.html.eex",
    "../lib/**/*.html.leex",
    "../lib/**/*.ex",
    "./js/**/*.js"
  ],

...

  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
    require('@tailwindcss/aspect-ratio')
  ]
```

### Configure Webpack to use PostCSS

PostCSS is added to the asset pipeline by specifying it in `assets\webpack.config.js`. 

In the `webpack.confi.js` file, find the `module:{ rules: [ ...` section and add `postcss-loader` immediately following `css-loader`. If you are using Sass, `sass-loader` will follow `postcss-loader`. (Note: order is important)
```javascript
// webpack.config.js
   ...
        {
          test: /\.[s]?css$/,
          use: [
            MiniCssExtractPlugin.loader,
            'css-loader',
            'postcss-loader'
          ],
        }
```

### Create PostCSS config file

```bash
touch postcss.config.js
```

```javascript
// postcss.config.js
module.exports = {
  plugins: [
    require('postcss-import'),
    require('tailwindcss'),
    require('autoprefixer'),
  ]
}
```

Up to this point you have configured your asset pipeline by configuring webpack to call `css-loader`-> `postcss-loader` and postcss to call `postcss-import` -> `tailwindcss` -> `autoprefixer`.

### Configure `app.css`

Put the default phx project CSS in its own `live_view.css` file.

```bash
# cwd assets/
cd css
mv app.scss live_view.css
touch app.css
rm phoenix.css
rm ../static/images/phoenix.png
```

Remove the line `@import "./phoenix.css";` from `live_view.css`.

Change the `app.scss` reference in `app.js` to `app.css`.

```javascript
// app.js
// import "../css/app.scss"
import "../css/app.css"
```

```css
/* app.css */
@import "tailwindcss/base";
@import "tailwindcss/components";
@import "tailwindcss/utilities";
@import "live_view.css";
/* @import "other-custom.css"; */
```

### Update the deploy script.

```javascript
// package.json
"scripts": {
    "deploy": "NODE_ENV=production webpack --mode production",
    ...
  },
```

### Update Dependencies
```bash
cd ../..
mix deps.get
```

### Test Your Config

You can test your setup by running a production and development build of the app assets. A development build will have a `priv/static/css/app.css` that is 3+MB in size while a production build will have a `priv/static/css/app.css` under 10KB.

```bash
cd assets
npm i
npm run deploy # check size of app.css
./node_modules/webpack/bin/webpack.js --mode development
cd ..
mix phx.digest
```

## Using TailwindCSS JIT

A few notes on using JIT and TailwindCSS.
There appear to be caching issues with Tailwind JIT and updates not being recognized. I haven't investigated this in detail yet, but below are the basic instructions for running TailwindCSS `JIT`.

Enable `JIT` mode in your Tailwind config file.
```javascript
// tailwind.config.js
module.exports = {
  mode: 'jit',
  purge: [
  ...
```

Clear the `.cache` directory in `node_modules`

```bash
rm -rf assets/node_modules/.cache/
```

Launch the Phoenix server with

```bash
NODE_ENV=development mix phx.server
```
or
```bash
NODE_ENV=development iex -S mix phx.server
```

If you have any information on running `JIT`, I would love to hear from you.

----
For more information on setting up TailwindCSS, see the [excellent article by Mike Clark](https://pragmaticstudio.com/tutorials/adding-tailwind-css-to-phoenix)


