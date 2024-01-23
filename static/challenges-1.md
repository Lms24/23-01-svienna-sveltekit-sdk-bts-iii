## 2. Challenges

---

### 1. Instrumenting `load` functions

We want to instrument `load` functions for:
- Performance/timing
- Errors
- Distributed tracing

```js []
// +page.ts
export const load = async ({ fetch }) => {
    const data = await fetch('/api/messages');
    const messages = await data.json();
    return { messages };
}
```
<!-- .element: class="fragment" -->

---
Naive: Just start a span

```js [8-10|2|4-7,11]
// +page.ts
import { startSpan } from '@sentry/sveltekit'

export const load = async ({ fetch }) => startSpan({
    name: 'universal load span /messages',
    op: 'function.sveltekit.load'
}, () => {
    const data = await fetch('/api/messages');
    const messages = await data.json();
    return { messages };
});
```

---
Pragmatic: Provide `load` wrapper

```js [5-7|2|4,8]
// +page.ts
import { wrapLoadWithSentry } from '@sentry/sveltekit'

export const load = wrapLoadWithSentry(async ({ fetch }) => {
    const data = await fetch('/api/messages');
    const messages = await data.json();
    return { messages };
})
```

---

Ideal: No Sentry code at all

```js []
// +page.ts
export const load = async ({ fetch }) => {
    const data = await fetch('/api/messages');
    const messages = await data.json();
    return { messages };
}
```

[How?](https://github.com/getsentry/sentry-javascript/blob/66333e694207ba544e852679833bd40be4cc3e17/packages/sveltekit/src/vite/sentryVitePlugins.ts#L78)

---

<img src="/load-waterfall.png" alt="github issue" class="border-gray-500 border-[1px]" />

---

### 2. Instrumenting client-side `fetch`

in `load` functions


```js [2]
// +page.ts
export const load = async ({ fetch }) => {
    const data = await fetch('/api/messages');
    const messages = await data.json();
    return { messages };
}
```

---

- SvelteKit adds additional behaviour
  - Warnings in dev mode
  - Caching of load results
  
- We want to track `fetch` falls in load functions
  - Timing
  - Distributed Tracing


- Timing problem!


