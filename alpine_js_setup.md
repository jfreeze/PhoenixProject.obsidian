Change this to an npm install.

Add the following to `app.js`.

```javascript
// app.js
import "alpinejs"

// let liveSocket = new LiveSocket("/live", Socket, { params: { _csrf_token: csrfToken } })
let liveSocket = new LiveSocket("/live", Socket, {
    dom: {
        onBeforeElUpdated(from, to) {
        if (from.__x) { window.Alpine.clone(from.__x, to) }
        }
    },
    params: { _csrf_token: csrfToken }
})
```


Import Alpine in `root.html.leex`. Note, get the latest version of AlpineJS from [https://github.com/alpinejs/alpine](https://github.com/alpinejs/alpine#install).

```html
<body class="font-sans">
   <script src="https://cdn.jsdelivr.net/gh/alpinejs/alpine@v2.x.x/dist/alpine.min.js" defer></script>
   <%= @inner_content %>
</body>
```

