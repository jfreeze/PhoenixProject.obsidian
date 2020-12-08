Install Alpine JS


```bash
cd assets
npm install alpinejs
```

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


