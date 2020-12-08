Topbar
[[http://buunguyen.github.io/topbar/]]

```bash
cd assets
npm i topbar
```

Add topbar to `app.js` and replace `page-loading` event listeners.
```javascript
// app.js
// import NProgress from "nprogress"
import Topbar from 'topbar'
let pageLoadTimeoutId = null
window.addEventListener('phx:page-loading-start', info => {
  clearTimeout(pageLoadTimeoutId)
  pageLoadTimeoutId = setTimeout(() => Topbar.show(), 200)
})
window.addEventListener('phx:page-loading-stop', info => {
  clearTimeout(pageLoadTimeoutId)
  Topbar.hide()
})
```

Remove `nprogress.css` import from `live_view.css`

```
/* live_view.css */
delete => @import "../node_modules/nprogress/nprogress.css";
```