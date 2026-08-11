# U.S. Department of the Treasury Exchange Rate API client

Official **U.S. Department of the Treasury** (the United States) quarterly exchange rates in Node.js / TypeScript — 145 currencies against the USD, with history back to 2001. Zero dependencies, works in Node 18+, Bun, Deno, and edge runtimes (uses global `fetch`).

These are the *published tax authority rates* required for tax filings, customs valuations, audits, and compliant invoicing — not moving market rates. Every response carries the publisher's own publication date.

Powered by [AllRatesToday](https://allratestoday.com/tax-authority-rates-api/ustreasury/). Get a free API key at [allratestoday.com/register](https://allratestoday.com/register) — no credit card required.

## Install

```bash
npm install ustreasury-exchange-rate
```

## Quick start

```js
import { getRate, getLatestRates } from 'ustreasury-exchange-rate';

// One pair at the official U.S. Department of the Treasury rate
const pair = await getRate('USD', 'EUR', { apiKey: 'art_live_...' });
console.log(pair.rate, pair.rate_date); // e.g. USD -> EUR on the bank's own date

// The bank's full published table
const table = await getLatestRates({ apiKey: 'art_live_...' });
console.log(table.rate_date, table.rates.length);
```

## Historical data (paid plans)

```js
import { getRatesForDate, getHistory } from 'ustreasury-exchange-rate';

// The official table for an invoice date — weekends/holidays return the
// most recent published date, flagged via published_on_requested_date.
const day = await getRatesForDate('2026-06-30', { apiKey: 'art_live_...' });

// Daily series for one pair
const series = await getHistory(
  { source: 'USD', target: 'EUR', from: '2026-01-01' },
  { apiKey: 'art_live_...' }
);
```

## Currencies covered

U.S. Department of the Treasury currently publishes rates covering **146 currencies** (as of the latest table):

`AED` · `AFN` · `ALL` · `AMD` · `AOA` · `ARS` · `AUD` · `AZN` · `BAM` · `BBD` · `BDT` · `BGN` · `BHD` · `BIF` · `BMD` · `BND` · `BOB` · `BRL` · `BSD` · `BWP` · `BYN` · `BZD` · `CAD` · `CDF` · `CHF` · `CLP` · `CNY` · `COP` · `CRC` · `CUC` · `CUP` · `CVE` · `CZK` · `DJF` · `DKK` · `DOP` · `DZD` · `EGP` · `ERN` · `ETB` · `EUR` · `FJD` · `GBP` · `GEL` · `GHS` · `GMD` · `GNF` · `GTQ` · `GYD` · `HKD` · `HNL` · `HTG` · `HUF` · `IDR` · `ILS` · `INR` · `IQD` · `IRR` · `ISK` · `JMD` · `JOD` · `JPY` · `KES` · `KGS` · `KHR` · `KMF` · `KRW` · `KWD` · `KYD` · `KZT` · `LAK` · `LBP` · `LKR` · `LRD` · `LSL` · `LYD` · `MAD` · `MDL` · `MGA` · `MKD` · `MMK` · `MNT` · `MRU` · `MUR` · `MVR` · `MWK` · `MXN` · `MYR` · `MZN` · `NAD` · `NGN` · `NIO` · `NOK` · `NPR` · `NZD` · `OMR` · `PEN` · `PGK` · `PHP` · `PKR` · `PLN` · `PYG` · `QAR` · `RON` · `RSD` · `RUB` · `RWF` · `SAR` · `SBD` · `SCR` · `SDG` · `SEK` · `SGD` · `SLE` · `SOS` · `SRD` · `SSP` · `STN` · `SYP` · `SZL` · `THB` · `TJS` · `TMT` · `TND` · `TOP` · `TRY` · `TTD` · `TWD` · `TZS` · `UAH` · `UGX` · `USD` · `UYU` · `UZS` · `VES` · `VND` · `VUV` · `WST` · `XAF` · `XCD` · `XCG` · `XOF` · `YER` · `ZAR` · `ZMW` · `ZWG`

Pairs the tax authority does not print directly are resolved from this table (see below).

## Published vs derived rates

If U.S. Department of the Treasury does not print a pair directly, the API resolves it from the bank's table (inverse, or a cross rate via USD) and flags it `derived: true` with the `method` — so official and computed values are never confused.

## Notes

- Every request counts toward your AllRatesToday monthly quota. Rates change once per business day — cache a day's table locally and a small quota goes a long way.
- Latest rates are on every plan (including free); historical dates and time series need a [paid plan](https://allratestoday.com/pricing/).
- Full API reference: [allratestoday.com/docs#central-bank](https://allratestoday.com/docs/#central-bank) · All covered sources: [tax authority rates API](https://allratestoday.com/tax-authority-rates-api/)

## License

MIT
