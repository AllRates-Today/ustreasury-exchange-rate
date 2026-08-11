# U.S. Department of the Treasury Exchange Rate API — ustreasury-exchange-rate

[![npm version](https://img.shields.io/npm/v/ustreasury-exchange-rate.svg)](https://www.npmjs.com/package/ustreasury-exchange-rate)
[![license](https://img.shields.io/npm/l/ustreasury-exchange-rate.svg)](https://github.com/AllRates-Today/ustreasury-exchange-rate/blob/main/LICENSE)
[![zero dependencies](https://img.shields.io/badge/dependencies-0-brightgreen.svg)](https://www.npmjs.com/package/ustreasury-exchange-rate)
[![TypeScript](https://img.shields.io/badge/TypeScript-types%20included-3178C6.svg)](https://www.typescriptlang.org/)

**Official U.S. Department of the Treasury (the United States) quarterly exchange rates for Node.js and TypeScript. The published tax authority rates behind tax filings, customs valuations, audits, and compliant invoicing — not market estimates, but the numbers U.S. Department of the Treasury itself prints, every quarter.**

## 🚀 Why this client?

- 🏛️ **Official published rates** — U.S. Department of the Treasury's own table, with the publisher's own `rate_date` on every response
- 📅 **History back to 2001** — point-in-time tables and daily series for any past date
- 🔀 **Published vs derived, always flagged** — computed inverse/cross pairs carry `derived: true`, never mixed with official prints
- ⚡ **Zero dependencies** — pure ESM + CJS over global `fetch`; Node 18+, Bun, Deno, and edge runtimes
- 🔷 **Type-safe** — full TypeScript definitions shipped with the package
- 🧾 **Compliance-grade metadata** — `rate_type`, publication date, and source disclaimer on every response

## 🔑 Get your API key

Get a free API key at [allratestoday.com/register](https://allratestoday.com/register) — no credit card required. Latest rates are on every plan, including free.

## 📦 Installation

```bash
npm install ustreasury-exchange-rate
```

```bash
yarn add ustreasury-exchange-rate
```

```bash
pnpm add ustreasury-exchange-rate
```

Also published under the org scope as [`@allratestoday/ustreasury-exchange-rate`](https://www.npmjs.com/package/@allratestoday/ustreasury-exchange-rate) — same code, same versions.

## 🏁 Quick start

```js
import { getRate } from 'ustreasury-exchange-rate';

const pair = await getRate('USD', 'EUR', { apiKey: 'art_live_...' });
console.log(pair.rate, pair.rate_date); // the official U.S. Department of the Treasury rate, on the tax authority's own date
```

## 📚 API reference

- [Latest pair rate](#latest-pair-rate) — one pair from the latest published table
- [Full published table](#full-published-table) — everything the tax authority printed, in one call
- [Table for a date](#table-for-a-date) — the official table for an invoice or filing date
- [Daily time series](#daily-time-series) — one pair across a date range

---

### Latest pair rate

Free plan and up. Pairs the tax authority does not print directly are resolved from its table and flagged (see *Published vs derived rates* below).

```js
const pair = await getRate('USD', 'EUR', { apiKey: 'art_live_...' });
```

**Response:**

```javascript
{
  bank: 'ustreasury',
  name: 'U.S. Department of the Treasury',
  rate_date: '2026-06-30',   // U.S. Department of the Treasury's own publication date
  source: 'USD',
  target: 'EUR',
  rate: 0.877,
  rate_type: 'quarterly',
  derived: false,
  method: 'published',
  disclaimer: '…'
}
```

### Full published table

Free plan and up. The complete table for the latest publication date.

```js
import { getLatestRates } from 'ustreasury-exchange-rate';

const table = await getLatestRates({ apiKey: 'art_live_...' });
console.log(table.rate_date, table.rates.length);
```

**Response:**

```javascript
{
  bank: 'ustreasury',
  name: 'U.S. Department of the Treasury',
  rate_date: '2026-06-30',
  rates: [
    { "base": "USD", "quote": "EUR", "type": "quarterly", "value": 0.877 },
    // … the rest of the published table (145 currencies vs USD)
  ],
  disclaimer: '…'
}
```

### Table for a date

Paid plans. The official table for any date since 2001 — weekends and holidays return the most recent published date, flagged via `published_on_requested_date`, which is exactly the in-force rate a filing needs.

```js
import { getRatesForDate } from 'ustreasury-exchange-rate';

const day = await getRatesForDate('2026-06-30', { apiKey: 'art_live_...' });
// Optionally narrow to one pair:
const one = await getRatesForDate('2026-06-30', { apiKey: 'art_live_...', source: 'USD', target: 'EUR' });
```

**Response:**

```javascript
{
  bank: 'ustreasury',
  requested_date: '2026-06-30',
  rate_date: '2026-06-30',                // the date actually published
  published_on_requested_date: true,      // false when a weekend/holiday fell back
  rates: [ /* the full table for that date */ ],
  disclaimer: '…'
}
```

### Daily time series

Paid plans. One resolved rate per publication date — ready for charting, revaluation runs, or audit workpapers.

```js
import { getHistory } from 'ustreasury-exchange-rate';

const series = await getHistory(
  { source: 'USD', target: 'EUR', from: '2026-01-01', to: '2026-06-30' },
  { apiKey: 'art_live_...' }
);
```

**Response:**

```javascript
{
  bank: 'ustreasury',
  source: 'USD',
  target: 'EUR',
  from: '2026-01-01',
  to: '2026-06-30',
  count: 152,
  rates: [
    // one entry per publication date
    { date: '2026-06-30', rate: 0.877, rate_type: 'quarterly', derived: false, method: 'published' },
    // …
  ],
  disclaimer: '…'
}
```

Pass `{ symbol: 'EUR' }` instead of `source`/`target` to get the raw published rows for one currency (all rate types, no pair resolution).

---

## 🗺️ Currencies covered

U.S. Department of the Treasury currently publishes rates covering **146 currencies** (as of the latest table):

`AED` · `AFN` · `ALL` · `AMD` · `AOA` · `ARS` · `AUD` · `AZN` · `BAM` · `BBD` · `BDT` · `BGN` · `BHD` · `BIF` · `BMD` · `BND` · `BOB` · `BRL` · `BSD` · `BWP` · `BYN` · `BZD` · `CAD` · `CDF` · `CHF` · `CLP` · `CNY` · `COP` · `CRC` · `CUC` · `CUP` · `CVE` · `CZK` · `DJF` · `DKK` · `DOP` · `DZD` · `EGP` · `ERN` · `ETB` · `EUR` · `FJD` · `GBP` · `GEL` · `GHS` · `GMD` · `GNF` · `GTQ` · `GYD` · `HKD` · `HNL` · `HTG` · `HUF` · `IDR` · `ILS` · `INR` · `IQD` · `IRR` · `ISK` · `JMD` · `JOD` · `JPY` · `KES` · `KGS` · `KHR` · `KMF` · `KRW` · `KWD` · `KYD` · `KZT` · `LAK` · `LBP` · `LKR` · `LRD` · `LSL` · `LYD` · `MAD` · `MDL` · `MGA` · `MKD` · `MMK` · `MNT` · `MRU` · `MUR` · `MVR` · `MWK` · `MXN` · `MYR` · `MZN` · `NAD` · `NGN` · `NIO` · `NOK` · `NPR` · `NZD` · `OMR` · `PEN` · `PGK` · `PHP` · `PKR` · `PLN` · `PYG` · `QAR` · `RON` · `RSD` · `RUB` · `RWF` · `SAR` · `SBD` · `SCR` · `SDG` · `SEK` · `SGD` · `SLE` · `SOS` · `SRD` · `SSP` · `STN` · `SYP` · `SZL` · `THB` · `TJS` · `TMT` · `TND` · `TOP` · `TRY` · `TTD` · `TWD` · `TZS` · `UAH` · `UGX` · `USD` · `UYU` · `UZS` · `VES` · `VND` · `VUV` · `WST` · `XAF` · `XCD` · `XCG` · `XOF` · `YER` · `ZAR` · `ZMW` · `ZWG`

## ⚖️ Published vs derived rates

If U.S. Department of the Treasury does not print a pair directly, the API resolves it from the tax authority's own table and says so — official and computed values are never confused:

| `method` | `derived` | Meaning |
| --- | --- | --- |
| `published` | `false` | The tax authority printed this pair directly |
| `inverse` | `true` | Computed as 1 ÷ the published opposite direction |
| `cross` | `true` | Computed via USD from two published rates |

## 🛡️ Error handling

Errors are thrown as `Error` with `status` (HTTP code) and `body` (the API's JSON error) attached:

```js
try {
  const pair = await getRate('USD', 'XXX', { apiKey: 'art_live_...' });
} catch (err) {
  console.log(err.message); // human-readable reason
  console.log(err.status);  // e.g. 404
}
```

| Status | Meaning |
| ------ | ------- |
| — | Missing `apiKey` (thrown before any request) |
| `400` | Malformed date or parameters |
| `401` | Invalid API key |
| `403` | Endpoint needs a [paid plan](https://allratestoday.com/pricing/) (historical dates & series) |
| `404` | Pair or date range not covered by U.S. Department of the Treasury |
| `429` | Monthly quota exceeded |

## 🔷 TypeScript

Full definitions ship with the package — no `@types` install:

```ts
import type { LatestRates, PairRate, DatedRates, RateEntry, HistoryQuery, RequestOptions } from 'ustreasury-exchange-rate';
```

## 📦 CommonJS

```javascript
const { getRate } = require('ustreasury-exchange-rate');

getRate('USD', 'EUR', { apiKey: 'art_live_...' }).then((pair) => console.log(pair.rate));
```

## 💡 Quota tips

- Rates change once a quarter — cache the published table locally and a small monthly quota goes a long way.
- Every request counts toward your AllRatesToday quota, shared across all AllRatesToday endpoints on your key.

## 📖 Methods reference

| Method | Plan | Description |
| ------ | ---- | ----------- |
| `getRate(source, target, { apiKey })` | Free | Latest rate for one pair, resolved from the published table |
| `getLatestRates({ apiKey })` | Free | The tax authority's full latest published table |
| `getRatesForDate(date, { apiKey, source?, target? })` | Paid | The official table (or one pair) for a YYYY-MM-DD date |
| `getHistory({ symbol \| source+target, from?, to? }, { apiKey })` | Paid | Daily series since 2001 |

## 🔗 Links

- [U.S. Department of the Treasury rates page](https://allratestoday.com/tax-authority-rates-api/ustreasury/) — live table, publication cadence, FAQ
- [All tax authority sources](https://allratestoday.com/tax-authority-rates-api/)
- [API documentation](https://allratestoday.com/docs/#central-bank) · [Interactive reference](https://allratestoday.com/api-reference/)
- [Register (free)](https://allratestoday.com/register) · [Pricing](https://allratestoday.com/pricing/)
- [GitHub](https://github.com/AllRates-Today/ustreasury-exchange-rate)

## 📜 License

MIT
