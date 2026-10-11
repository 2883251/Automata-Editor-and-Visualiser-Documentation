# Performance

Lab measurements from [PageSpeed Insights](https://pagespeed.web.dev/) for the deployed frontend at `https://automata-ev-gghea7fxbsh9b5hx.southafricanorth-01.azurewebsites.net/`.

**Report date:** 11 October 2026
**Lighthouse:** 13.5.0
**Field data:** Not available — the Chrome UX Report has insufficient real-world data for this page, so only lab results are shown.

---

## Desktop

**Test environment:** Emulated Desktop, custom throttling

### Scores

| Category | Score |
|---|---|
| **Performance** | **56 / 100** |
| Accessibility | 100 / 100 |
| Best Practices | 100 / 100 |
| SEO | 82 / 100 |

### Core Web Vitals & Metrics

| Metric | Value | Rating |
|---|---|---|
| First Contentful Paint (FCP) | 5.5 s | Poor |
| Largest Contentful Paint (LCP) | 5.5 s | Poor |
| Total Blocking Time (TBT) | 60 ms | Good |
| Speed Index (SI) | 5.5 s | Poor |
| Cumulative Layout Shift (CLS) | 0.039 | Good |
| Time to Interactive (TTI) | 5,688 ms | — |

### Diagnostics

| Issue | Detail |
|---|---|
| Reduce unused JavaScript | Est. savings of 3,872 KiB |
| Avoid enormous network payloads | Total size was 5,052 KiB |
| Avoid long main-thread tasks | 4 long tasks found |

### Opportunities

| Opportunity | Est. Savings |
|---|---|
| Render-blocking requests | 240 ms |

---

## Mobile

**Test environment:** Emulated Moto G Power, Slow 4G throttling

### Scores

| Category | Score |
|---|---|
| **Performance** | **53 / 100** |
| Accessibility | 100 / 100 |
| Best Practices | 100 / 100 |
| SEO | 82 / 100 |

### Core Web Vitals & Metrics

| Metric | Value | Rating |
|---|---|---|
| First Contentful Paint (FCP) | 27.6 s | Poor |
| Largest Contentful Paint (LCP) | 27.6 s | Poor |
| Total Blocking Time (TBT) | 50 ms | Good |
| Speed Index (SI) | 27.6 s | Poor |
| Cumulative Layout Shift (CLS) | 0.09 | Good |
| Time to Interactive (TTI) | 27,660 ms | Poor |

### Diagnostics

| Issue | Detail |
|---|---|
| Reduce unused JavaScript | Est. savings of 3,872 KiB |
| Avoid enormous network payloads | Total size was 5,052 KiB |
| Avoid long main-thread tasks | 3 long tasks found |

### Opportunities

| Opportunity | Est. Savings |
|---|---|
| Render-blocking requests | 1,500 ms |

### SEO Issues

- Document does not have a meta description
- robots.txt is not valid (25 errors found)

---

## Summary

| Metric | Desktop | Mobile |
|---|---|---|
| Performance | 56 | 53 |
| Accessibility | 100 | 100 |
| Best Practices | 100 | 100 |
| SEO | 82 | 82 |
| FCP | 5.5 s | 27.6 s |
| LCP | 5.5 s | 27.6 s |
| TBT | 60 ms | 50 ms |
| SI | 5.5 s | 27.6 s |
| CLS | 0.039 | 0.09 |
| TTI | 5,688 ms | 27,660 ms |
| Total page weight | 5,052 KiB | 5,052 KiB |
| Unused JS (potential savings) | 3,872 KiB | 3,872 KiB |
| Render-blocking savings | 240 ms | 1,500 ms |

### Key observations

- **Accessibility and Best Practices are perfect** (100/100) on both form factors.
- **Performance is the weakest category** (56 desktop, 53 mobile), dragged down by slow FCP, LCP, and Speed Index.
- **TBT and CLS are good** on both — the main thread is not heavily blocked, and layout is stable.
- **The biggest opportunity** is reducing unused JavaScript (~3.9 MB potential savings), which accounts for most of the 5 MB total page weight.
- **Render-blocking resources** add 240 ms on desktop and 1,500 ms on mobile.
- **Mobile is significantly slower** than desktop due to Slow 4G throttling (27.6 s vs 5.5 s for FCP/LCP/SI).
- **SEO at 82** is held back by a missing meta description and an invalid robots.txt.

---

## API Performance

Load test results from [Artillery](https://www.artillery.io/) against the deployed backend at `https://automata-ev-server-dwengvgxevg3hpbz.southafricanorth-01.azurewebsites.net`.

**Test date:** 11 October 2026
**Duration:** 120 seconds (~2 minutes)
**Request rate:** 9 requests/second sustained
**Total virtual users:** 390
**Success rate:** 100% (0 failed virtual users)

### Overall Statistics

| Metric | Value |
|---|---|
| Total HTTP requests | 1,784 |
| HTTP 200 (OK) | 1,478 |
| HTTP 201 (Created) | 153 |
| HTTP 204 (No Content) | 153 |
| Total errors | 0 |
| Data transferred | 651,814 bytes (~637 KB) |

### Response Time Performance

**Overall HTTP response times:**

| Percentile | Value |
|---|---|
| Minimum | 5 ms |
| Median (p50) | 117.9 ms |
| p75 | 278.7 ms |
| p90 | 699.4 ms |
| p95 | 944 ms |
| p99 | 1,620 ms |
| p99.9 | 2,018.7 ms |
| Maximum | 2,169 ms |
| Mean | 248 ms |

**Session length (per virtual user):**

| Percentile | Value |
|---|---|
| Minimum | 23 ms |
| Median (p50) | 596 ms |
| p75 | 1,224.4 ms |
| p90 | 2,893.5 ms |
| p95 | 4,867 ms |
| p99 | 7,557.1 ms |
| Maximum | 8,225.5 ms |
| Mean | 1,158.7 ms |

### Per-Endpoint Performance

| Endpoint | Requests | Mean (ms) | Median (ms) | p95 (ms) | p99 (ms) |
|---|---|---|---|---|---|
| `/health` | 76 | 74.4 | 26.8 | 314.2 | 518.1 |
| `/api/mark` | 805 | 181.3 | 89.1 | 645.6 | 1,176.4 |
| `/api/machines` | 222 | 331.7 | 165.7 | 1,107.9 | 1,755 |
| `/api/machines/{id}` | 444 | 316.3 | 141.2 | 1,002.4 | 1,720.2 |
| `/api/machines/shared-with-me` | 111 | 321.5 | 179.5 | 963.1 | 1,408.4 |
| `/api/api-keys` | 84 | 338.2 | 133 | 1,022.7 | 1,686.1 |
| `/api/api-keys/{id}` | 42 | 301.3 | 149.9 | 982.6 | 1,022.7 |

### Test Scenarios

| Flow | Virtual Users | Percentage |
|---|---|---|
| markEndpoint | 161 | 41.3% |
| machineCrud | 111 | 28.5% |
| healthCheck | 76 | 19.5% |
| apiKeyManagement | 42 | 10.8% |

### Key observations

- **100% success rate** — all 390 virtual users completed with zero errors, no socket timeouts and no DNS failures.
- **Median response time is 118 ms** — half of all requests finished in under 120 ms.
- **Fastest endpoint:** `/health` at 26.8 ms median.
- **Slowest endpoint:** `/api/api-keys` at 338.2 ms mean; database-heavy operations (machines, API keys) cluster around 300–340 ms.
- **Highest traffic:** `/api/mark` handled 805 requests (45% of all responses), the core marking functionality.
- **Tail latency is moderate** — 1% of requests exceeded 1,620 ms, with the worst case at 2,169 ms.
- **Complete user sessions** (a full CRUD flow or mark run) took a median of 596 ms end-to-end.

---

**AI Declaration:** The preceding document was generated with the assistance of: Qoder-IDE [Efficient].
