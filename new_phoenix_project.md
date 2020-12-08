# New Phoenix Project Setup with TailwindCSS

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
This installs TailwindCSS 2.0 as a PostCSS plugin. Several first-party plugins are included here if you need them. It is ok to leave them in since they are purged from the production release if not used.
```bash
cd assets

npm install tailwindcss \
   @tailwindcss/forms \
   @tailwindcss/typography \
   @tailwindcss/aspect-ratio \
   postcss postcss-loader postcss-import \
   autoprefixer --save-dev

npm install alpinejs

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

#### Create `postcss.config.js`

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

#### Configure `app.css`

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
```

#### Update the deploy script.

```javascript
// package.json
"scripts": {
    "deploy": "NODE_ENV=production webpack --mode production",
    ...
  },
```

#### Add `postcss-loader` to  WebPack (`webpack.config.js`)

Find the `module:{ rules: [ ...` section in `webpack.config.js` and add `postcss-loader`. (Note: order is important)
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


#### Update Dependencies
```bash
cd ../..
mix deps.get
```

#### Test Your Config

```bash
cd assets
npm i
npm run deploy # check size of app.css
./node_modules/webpack/bin/webpack.js --mode development
cd ..
mix phx.digest
```





