# Browser Validation

For UI goals, **always activate the `agent-browser` skill** before performing QA. Do not skip live
browser validation just because `tsc`, `lint`, or `npm run build` passed — rendered output must be
confirmed in a real browser.

## Step 0 — bootstrap agent-browser

Before any other step, ensure the CLI and Chrome are present. Run this block:

```bash
if ! command -v agent-browser &>/dev/null; then
  npm install -g agent-browser
fi
if ! agent-browser doctor --offline --quick 2>&1 | grep -q "Chrome.*ok"; then
  agent-browser install
fi
agent-browser skills get dogfood
```

If install fails, record `browser_tools_unavailable: true` in **Skipped checks**, dock
`verificationStrength` by 0.15 in `judge.json`, and note the error. Do not silently skip.

## Primary tool: dogfood skill

For any UI goal, use the **`dogfood`** skill rather than the bare `core` skill. Dogfood performs
systematic exploratory QA — it checks the console, records repro evidence, and catches issues that
a simple navigate-and-screenshot pass will miss (broken images, failed network requests, JS errors).

```bash
agent-browser skills get dogfood    # load the full dogfood workflow
```

Run dogfood against the dev server. Write its output report to `.goal/dogfood-output/`.

## Required checks for every UI QA pass

Run all of these — do not skip any:

### 1. Console errors
```bash
agent-browser console    # must return no error-level entries
agent-browser errors     # must return empty
```
If there are console errors, record them as failures in `verification.md` and set `decision: repeat` in `judge.json`.

### 2. Network failures
```bash
agent-browser network requests    # inspect for 4xx/5xx responses
```
Any failed resource request (image, font, script) is a defect, not a warning.

### 3. Image load assertion
After navigating to the page, **always** assert that images actually loaded — do not infer this
from the accessibility tree or from `<img>` elements being present in the DOM. An `<img>` in the
DOM with a broken src will be present in a snapshot but will show a broken icon in the browser.

Run this eval to count images that failed to load:
```bash
agent-browser eval "Array.from(document.querySelectorAll('img')).filter(i => !i.complete || i.naturalWidth === 0).length"
```
This must return `0`. If it returns anything greater than zero, images are broken — record the count
as a failure, investigate the src URLs, and set `decision: repeat` in `judge.json`.

Also capture an annotated screenshot so broken image placeholders are visually documented:
```bash
agent-browser screenshot --annotate .goal/dogfood-output/screenshots/images-check.png
```

### 4. Full-page screenshot
```bash
agent-browser screenshot --full .goal/dogfood-output/screenshots/full-page.png
```
Inspect the screenshot visually. If image slots are blank, white, or show broken-image icons, that
is a failure even if the eval above returned 0 (some browsers complete `<img>` loads with an error
state where `complete` is true but `naturalWidth` is 0 — the screenshot is the ground truth).

### 5. Heading and content assertions
```bash
agent-browser get text e1     # assert h1 text matches expected value
agent-browser eval "document.querySelectorAll('<card-selector>').length"   # assert card count ≥ minimum
```

## Evidence format

Add a **Browser validation** section to `.goal/verification.md`:

```markdown
## Browser validation

Skill: agent-browser dogfood  
Session: <session-name>  
Dev server: <url>

| Step | Command | Result |
| --- | --- | --- |
| Open root route | agent-browser open <url> | ✅ Page title: "..." |
| Console errors | agent-browser console | ✅ None |
| Network failures | agent-browser network requests | ✅ No 4xx/5xx |
| Broken images | eval naturalWidth===0 count | ✅ 0 broken |
| Screenshot | agent-browser screenshot --full | ✅ [images visible] |
| Assert heading | agent-browser get text e1 | ✅ "..." |
| Assert card count | eval querySelectorAll count | ✅ N |
```

## Failing fast

If **any** of the following are true, stop QA immediately, record the failure, and set `decision: repeat`:
- `agent-browser errors` returns non-empty output
- broken image eval returns > 0
- screenshot shows blank/broken image slots
- any network request returns 4xx or 5xx for a page resource (not an analytics/tracking call)

## If browser tools are unavailable

Record `browser_tools_unavailable: true` in **Skipped checks**, dock `verificationStrength` by at
least 0.15 in `judge.json`, and list it as a gap. Never mark a browser check as passing by inference.
