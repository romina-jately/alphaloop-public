# AlphaLoop public telemetry

Static showcase site for AlphaLoop, an AI research analyst that makes investment calls, keeps score of whether it was right, and learns from its misses. Served via GitHub Pages from this repo. The audience is a reader with forty seconds: the page leads with the live scoreboard, not the architecture.

## Files

- `index.html` is the hand-authored showcase page. Fully self-contained: system fonts, inline CSS and JS, inline SVG chart, no external requests. Section order: hook, live scoreboard, confidence-vs-results chart, lessons feed, dashboard screenshots, how the loop works, guardrails, under the hood.
- `alphaloop.json` is the sanitized aggregate snapshot, republished by the private repo's CI. It contains aggregates only: scoreboard counts, hit rates per group, benchmark comparison, owner-curated lessons, run health, and rejection counts. Holdings, tickers, the calls themselves, and model-authored text never leave the private repository.
- `shots/` (optional) holds three sanitized dashboard screenshots taken from a demonstration copy seeded with sample data: `daily.png`, `track-record.png`, `chat.png`. When a file is absent its slot hides itself; when all three are absent the section shows an honest placeholder instead. Each screenshot links to the matching page of the interactive demo.
- `demo/` is a static, click-through demo of the private dashboard: `index.html` (the money home), `calls.html` (the book of calls), `watching.html` (where the loop's attention is), `ask.html` (the grounded chat, including a reply withheld by the number gate), plus a shared `demo.css`. The pages are fully self-contained (system fonts, no JS, no external requests) and every number and ticker on them is invented fixture data, the same fictional seed the private repo uses for screenshots and tests. Nothing real ships.

## Data contract consumed by index.html

Top-level keys of `alphaloop.json` (all optional; missing or null values render as honest empty states, never `NaN` or `undefined`):

- `generated_at`: ISO timestamp shown in the stamp line.
- `scoreboard`: `{ calls_total, calls_graded, calls_open, days_running, validators_caught }`.
- `hit_rates`: `{ long_term: { graded, hit, rate }, quick_trades: { graded, hit, rate } }` with `rate` as a 0..1 fraction.
- `benchmark`: `{ avg_excess_vs_spy, graded_with_benchmark }` with `avg_excess_vs_spy` as a 0..1-scale fraction (0.021 renders as 2.1 percentage points).
- `lessons`: array of owner-curated strings. Only this array is ever rendered as lessons.
- `calibration`: per-group list of `{ bucket, n, hits, hit_rate }` used to draw the confidence chart.
- `theses`, `rejections`, `runs`, `lessons_count`: legacy aggregate keys; the page can derive the scoreboard and hit rates from them when the newer keys are absent.

## Fallback behavior

The page fetches `alphaloop.json` with `cache: no-store`. If the fetch fails (including when the page is opened directly from `file://`), it renders an embedded last-known snapshot and the stamp line reads "Live data unavailable. Showing last saved snapshot from <time>." The stamp is always set, in success, fallback, and error paths alike.

Recommendations only. AlphaLoop never places or manages any position. Not financial advice.
