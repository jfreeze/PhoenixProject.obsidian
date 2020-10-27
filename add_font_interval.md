Change `<head>` in `root.html.leex` and add inter val font.

```html
<link rel="stylesheet" href="https://rsms.me/inter/inter.css">
```

Add the font to `tailwind.config.js`.

```javascript
const defaultTheme = require('tailwindcss/defaultTheme')
  module.exports = {
    ...
    theme: {
      extend: {
        fontFamily: {
          sans: ['Inter var', ...defaultTheme.fontFamily.sans],
        },
```

