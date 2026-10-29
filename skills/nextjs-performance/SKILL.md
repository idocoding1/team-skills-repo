---
name: nextjs-performance
description: Diagnoses and fixes performance problems in Next.js App Router applications, covering bundle size, server vs client components, data fetching, caching, images, fonts, and Core Web Vitals. Use when a Next.js page is slow, a Lighthouse or Web Vitals score regressed, or the user asks how to optimize a Next.js app.
license: MIT
---

# Next.js Performance

Improve real, measurable performance of a Next.js application. Optimize based on
evidence, change one thing at a time, and confirm each improvement with a measurement.

## Step 1: Establish a baseline

Before changing code, collect at least one of the following and record the numbers:

- A production build output (`next build`). Note the route table: first-load JS per
  route and which routes are static, dynamic, or partially prerendered.
- Lighthouse or PageSpeed results for the slow page (LCP, INP, CLS, TTFB).
- Field data if available (Web Vitals reported by the app or analytics).

Always measure a production build, never the development server, which is
intentionally unoptimized.

Identify the main bottleneck category before acting:

| Symptom | Likely category |
|---|---|
| High TTFB | Server rendering, slow data fetching, missing caching |
| High LCP with low TTFB | Large hero image, render-blocking fonts or CSS, client-only rendering |
| High INP | Heavy client JavaScript, long tasks, expensive re-renders |
| High CLS | Images or embeds without dimensions, late-loading fonts, injected banners |
| Large first-load JS | Too many client components, heavy libraries in the client bundle |

## Step 2: Apply targeted fixes

### Server and client components

- Keep components as Server Components by default. Add `"use client"` only to the
  smallest interactive leaf that needs state, effects, or browser APIs.
- Do not mark a layout or page as a client component just to use one hook. Extract the
  interactive part into its own component.
- Pass serializable data from server to client components; do not import server-only
  modules (database clients, secrets) into client components. Use the `server-only`
  package to enforce this.

### Bundle size

- Find heavy dependencies with a bundle analyzer. Replace or lazy-load libraries that are
  large relative to their use (date libraries, charting, rich text editors, icon packs
  imported wholesale).
- Use `next/dynamic` for components that are below the fold, behind interaction, or
  browser-only (`ssr: false` only when truly necessary).
- Import only what you use (`import { debounce } from "lodash-es"`, not the whole
  package).

### Data fetching and caching

- Fetch data on the server, close to where it is used. Run independent requests in
  parallel with `Promise.all` instead of sequential `await`s (request waterfalls).
- Decide the caching behavior of each route deliberately: static, time-based
  revalidation, or dynamic. Check the current Next.js version's caching defaults in the
  official documentation before relying on them, because defaults have changed between
  major versions.
- Use streaming with `loading.tsx` or `<Suspense>` boundaries so slow data does not block
  the entire page.
- Deduplicate repeated data access in a single request (for example, with React `cache`).

### Images

- Use `next/image` with explicit `width`/`height` or `fill` plus a sized container, so the
  browser reserves space (prevents CLS).
- Mark the LCP image with `priority` (or the equivalent preload option in the installed
  version). Do not mark more than one or two images as priority.
- Provide accurate `sizes` for responsive images so the browser does not download
  oversized files.

### Fonts and CSS

- Use `next/font` to self-host fonts and avoid layout shift. Limit the number of weights
  and subsets.
- Avoid large global CSS imports for styles used on a single route.

### Third-party scripts

- Load analytics, chat widgets, and tag managers with `next/script` using
  `afterInteractive` or `lazyOnload`. Remove scripts that are no longer used.

### Rendering cost on the client

- Look for long tasks over 50 ms in a performance profile. Break up heavy work, memoize
  expensive derived data, and virtualize long lists.

## Step 3: Verify

Rebuild and re-measure using the same method as the baseline. Report:

```
## Baseline
<metric values and how they were measured>

## Changes
1. <change> — <why it addresses the bottleneck>

## Result
<new metric values, with the difference>

## Remaining opportunities
<ranked list, highest expected impact first>
```

If a change does not improve the metric it targeted, revert it rather than keeping
speculative complexity.
