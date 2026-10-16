# Image Validation

Any UI goal that renders images must explicitly verify that those images loaded successfully in the
browser. Static analysis (tsc, lint, build) cannot catch a broken image URL — the source may
compile cleanly but return a 4xx/5xx at runtime.

## The failure mode to guard against

An `<img>` element present in the DOM snapshot does **not** mean the image loaded. The accessibility
tree reports the element's presence and alt text regardless of load state. A screenshot taken
by a headless browser may show a white/blank area (not a broken-icon) when the image response is
a redirect the browser cannot follow, a CORS block, or a 4xx. This can be misread as "images are
loading, just not yet visible" — it is actually a defect.

## Mandatory image check

Run this after every page navigation that is expected to render images:

```bash
agent-browser eval "Array.from(document.querySelectorAll('img')).filter(i => !i.complete || i.naturalWidth === 0).length"
```

**Expected result: `0`**

If the result is > 0:
1. Identify which images failed:
   ```bash
   agent-browser eval "Array.from(document.querySelectorAll('img')).filter(i => !i.complete || i.naturalWidth === 0).map(i => i.src)"
   ```
2. Check whether the src URLs are reachable:
   ```bash
   curl -sIL "<src-url>" | grep -E "^HTTP|^content-type"
   ```
3. Record all broken src URLs in `verification.md` as failures.
4. Set `decision: repeat` in `judge.json` — broken images are a hard failure, not a gap.

## Wait before checking

Images may still be loading when the snapshot runs. Always wait for network idle before asserting:

```bash
agent-browser wait --load networkidle
agent-browser eval "Array.from(document.querySelectorAll('img')).filter(i => !i.complete || i.naturalWidth === 0).length"
```

## Third-party image services

When the implementation uses a third-party image placeholder service (e.g. placekitten.com,
picsum.photos, loremflickr.com), **always** verify the service is reachable before running QA:

```bash
curl -sIL "<image-service-base-url>/test-path" | grep "^HTTP"
```

If the service returns anything other than 2xx, treat it as a broken dependency and fix the image
source before continuing QA. Do not assume a service that worked at build time is still reachable.
