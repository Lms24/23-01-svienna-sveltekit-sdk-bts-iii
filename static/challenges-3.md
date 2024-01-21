### 3. Data Invalidation

![Route id](/load-waterfall-boxes.png)

---

We need to access `event.route.id`:

```js []
function wrapLoadWithSentry(event) {
    const routeId = event.route?.id;
    // ...
}
```

---

SvelteKit tracks accesses to properties of `load` events
  
➡ Accessing `route.id` invalidates data.
  
➡ We degraded our users' performance :(

---

#### Hack

```js []
function wrapLoadWithSentry(event) {
  const descriptor = 
    Object.getOwnPropertyDescriptor(event.route, 'id')
  const routeId = descriptor?.value;
  // ...
}
```

Problem: Requires special way of setting a proxy by Kit. 
<!-- .element: class="fragment" -->

---

#### Hack II

Contributed proxy usage to client side SvelteKit.

➡ Our hack works (`Kit >= 1.24.0`)

<a class="text-sm" href="https://github.com/sveltejs/kit/blob/main/packages/kit/src/runtime/client/client.js#L592">(Actual Code)</a>

---

#### Proper Solution

[`untrack` function](https://kit.svelte.dev/docs/load#rerunning-load-functions-untracking-dependencies) added in SvelteKit 2.0:

```js []
//TODO: Add to SDK
function wrapLoadWithSentry(event) {
  const routeId = event.untrack(() => event.route.id);
  // ...
}
```