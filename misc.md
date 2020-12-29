TailwindCSS Utility

```css
.\?\? {
  animation: \?\?wobble 0.5s ease-in-out alternate infinite;
}

@keyframes \?\?wobble {
  0% {
    box-shadow: inset 4px 4px rgb(236, 15, 170), inset -4px -4px rgb(236, 15, 170);
  }
  100% {
    box-shadow: inset 8px 8px rgb(236, 15, 170), inset -8px -8px rgb(236, 15, 170);
  }
}
```



Adding back Cyan

```javascript
# tailwind.config.js
const colors = require('tailwindcss/colors')

module.exports = {
  theme: {
    extend: {
      colors: {
        'light-blue': colors.lightBlue,
        cyan: colors.cyan,
      },
    },
  },
  variants: {},
  plugins: [],
}
```


