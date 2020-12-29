# New Phoenix Project Setup with TailwindCSS and PostCSS

Note, these install instructions modify the default Phoenix project by 

* adding TailwindCSS
* switch from SASS to CSS and add PostCSS

If you choose to use Sass, you can ignore the file renaming instructions, but will need to install sass-loader and add it to `assets/webpack.config.js`.

### Ensure Latest Version of Phoenix
`mix local.hex`

You can find the latest version of latest Pheonix at [Phoenix Installation](https://hexdocs.pm/phoenix/installation.html). Once you have the version, install Phoenix.
 
`mix archive.install hex phx_new 1.5.7`

### Create Your Project

```bash
mix phx.new --live my_app
cd my_app
mix ecto.create
```

### Install TailWindCSS
This installs TailwindCSS 2.0 as a PostCSS plugin. Several first-party plugins are included here if you need them. It is OK to leave them in since they are purged from the production release if not used. The next-to-last command installs the Tailwind config and the last command install the full version of the Tailwind config file that I use as a reference when needed.

```bash
cd assets

npm install tailwindcss \
   @tailwindcss/forms \
   @tailwindcss/typography \
   @tailwindcss/aspect-ratio \
   postcss postcss-loader postcss-import \
   autoprefixer --save-dev

npx tailwindcss init
npx tailwind init tailwindcss-full.js --full
```

### Customize TailWindCSS config
Add `purge` and `plugins`  to `tailwind.config.js`

```javascript
// tailwind.config.js
purge: {
    content: [
      "../lib/**/*.html.eex",
      "../lib/**/*.html.leex",
      "../lib/**/*.ex",
      "./js/**/*.js"
    ],

    // These options are passed through directly to PurgeCSS
    options: {
      // whitelist: ['opacity-75'],
    }
  },

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
    plugins: {
        'postcss-import': {},
        tailwindcss: {},
        autoprefixer: {}
    }
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


----
For more information on setting up TailwindCSS, see the [excellent article by Mike Clark](https://pragmaticstudio.com/tutorials/adding-tailwind-css-to-phoenix)


