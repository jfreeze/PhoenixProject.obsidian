Configure TailwindCSS to use Inter var font as the first `sans` font.

```javascript
// tailwind.config.js
const defaultTheme = require('tailwindcss/defaultTheme')
  module.exports = {
    ...
    theme: {
      extend: {
        fontFamily: {
          sans: ['Inter var', ...defaultTheme.fontFamily.sans],
        },
```

Add the Inter var font to `<head>` to the root HTML file.

```html
<!-- root.html.leex -->
<link rel="stylesheet" href="https://rsms.me/inter/inter.css">
```
