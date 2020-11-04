# New Phoenix Project Setup

### Ensure Latest Version of Phoenix
`mix local.hex`

You can find the latest version of latest Pheonix at [Phoenix Installation](https://hexdocs.pm/phoenix/installation.html). Once you have the version, install Phoenix.
 
`mix archive.install hex phx_new 1.5.6`

### Create Your Project

```bash
mix phx.new --live my_app
cd my_app
mix ecto.create
```

### Install TailWindCSS
This also installs a full version of the tailwindcss config file for reference.
```bash
cd assets
npm install tailwindcss @tailwindcss/typography @tailwindcss/ui postcss postcss-import postcss-loader --save-dev
npx tailwindcss init
npx tailwind init tailwindcss-full.js --full
```

### Customize TailWindCSS config
#### Add Purge to `tailwind.config.js`

_Note: Change the <my_app> to the name of your app directory!_

```javascript
  purge: [
    "../lib/<my_app>/**/*.ex",
    "../lib/<my_app>_web/**/*.ex",
    "../lib/<my_app>_web/**/*.html.eex",
    "../lib/<my_app>_web/**/*.html.leex",
    "./js/**/*.js"
  ],

  plugins: [
    require("@tailwindcss/ui")],
    require('@tailwindcss/typography'),
  ]

```

#### Create `postcss.config.js`

```bash
touch postcss.config.js
```
Contents of `postcss.config.js`:
```javascript
module.exports = {
  plugins: [
    require("postcss-import"),
    require('tailwindcss'),
    require('autoprefixer'),
    require('postcss-nested')
  ]
}
```

#### Add deploy script (`package.json`)

```javascript
  "scripts": {
    "deploy": "NODE_ENV=production webpack --mode production",
    ...
  },
```

#### Add `postcss-loader` to  WebPack (`webpack.config.js`)

Find the `module:{ rules: [ ...` section in `webpack.config.js` and add `postcss-loader`. (Note: order is important)
```javascript
        {
          test: /\.[s]?css$/,
          use: [
            MiniCssExtractPlugin.loader,
            'css-loader',
            'sass-loader',
            'postcss-loader'
          ],
        }
```


####  Setup `app.scss`

```bash
cd css
mv app.scss live_view.scss
rm phoenix.css
```
Remove the line `@import "./phoenix.css";` from `live_view.scss`.

#### Create `assets/css/app.scss`
```bash
touch app.scss
```
Contents of `app.scss`:
```scss
/* purgecss start ignore */

@import "tailwindcss/base";
@import "tailwindcss/components";

/* purgecss end ignore */

@import "tailwindcss/utilities";

@import "live_view.scss";
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
npm run deploy 
./node_modules/webpack/bin/webpack.js --mode development
cd ..
mix phx.digest
```





