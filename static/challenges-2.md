`start.js`

```js [1|3-6]
const native_fetch = window.fetch;

export const load_fetch = (url, opts) => {
    // SvelteKit's cache logic
    return native_fetch(url, opts);
}
```

<tiny class="text-sm"> 
    <a href="https://github.com/sveltejs/kit/blob/%40sveltejs/kit%401.25.0/packages/kit/src/runtime/client/fetcher.js">Actual Code</a>
</tiny>

---

`app.js`

```js [1-2,6]
// ...
// hooks.client.js

Sentry.init({
    //...
    integrations: [new BrowserTracing()]
});

const handleError = ...

// ...
```

---

`Sentry.BrowserTracing`

```js []
const originalFetch = window.fetch;

window.fetch = (url, opts) => Sentry.startSpan(() => {
    return originalFetch(url, opts);
})
```

---

### Hacks

1. Instrument fetch in `wrapLoadWithSentry`
   - +700 LoC :(
   
2. `FetchProxy` via HTML `<script>` 
   - Credits [@myieye](https://github.com/sveltejs/kit/issues/9530#issuecomment-1611195970)

---
<!-- .slide: data-visibility="hidden" -->
###### 1. Inject "Proxy" via HTML `<script>`:
```js [1|2|3]
const _fetch = window.fetch;
window.fetchProxy = (...args) => _fetch(...args);
window.fetch = (...args) => window.fetchProxy(...args);
```

---
<!-- .slide: data-visibility="hidden" -->
###### 2. SvelteKit creates its `load_fetch`   

`start.js`

```js [1|3-6]
const native_fetch = window.fetch;

export const load_fetch = (url, opts) => {
    // SvelteKit's cache logic
    return native_fetch(url, opts);
}
```

---
<!-- .slide: data-visibility="hidden" -->
###### 3. Swap during instrumentation:
   
```js [1|2|4|6|7]
const currFetch = window.fetch;
window.fetch = window.fetchProxy;

Sentry.init({}) // <-- instruments window.fetch

window.fetchProxy = window.fetch; 
window.fetch = currFetch;
```   

---
<!-- .slide: data-visibility="hidden" -->
###### 4. Profit!
   
- load `fetch` properly instrumented
- removed ~700 LoC (bundle size!)

---
<!-- .slide: data-visibility="hidden" -->
###### 5. Regret

- CSP (`<script>` tags)
- It's still a hack   
