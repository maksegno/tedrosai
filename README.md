# Tedros AI

**Every listing source. One search.** · [tedrosai.com](https://tedrosai.com)

Tedros AI is a real-estate market search tool. Type a US city or ZIP and it searches
Zillow, Redfin, Realtor.com and Apartments.com in parallel, merges duplicate listings,
flags homes priced differently across sites, and produces a market report with rental
yields and an AI-written brief.

This repository holds public documentation for the product. The application source is private.

## What a report includes

- For-sale listings de-duplicated across Zillow, Redfin and Realtor.com
- The same home's price on each site, with mismatches highlighted
- Median price, price per square foot and days on market
- Price-reduced listings
- Live apartment rents and gross rental yield by bedroom count
- A short market brief written by Google Gemini
- CSV export

## How it works

1. **Query:** the user enters a city and state or a ZIP code.
2. **Fetch, stage 1:** Zillow and Realtor.com are searched by place, in parallel.
3. **Fetch, stage 2:** a map bounding box is built from those results, and Redfin and
   Apartments.com are searched over the same area so every source covers the same ground.
4. **Merge:** addresses are normalized (unit markers, street suffixes, directions, ZIP)
   so the same home from different sites becomes a single record.
5. **Analyze:** market statistics and rent-based yields are computed from the merged data.
6. **Brief:** the computed facts, and only those facts, are passed to Gemini on Vertex AI
   for a three-to-four sentence summary.

A typical search completes in a few seconds.

## Architecture

```
Browser ──HTTPS──▶ Cloud Run (Go web app + API)
                     │
                     ├── Firestore ......... accounts, sessions, search history, quotas
                     ├── Vertex AI ......... Gemini market brief
                     └── Private VPC ──▶ Compute Engine ........ listing-data connectors
```

| Layer | Technology |
|---|---|
| Web app and API | Go on Google Cloud Run |
| Data | Cloud Firestore |
| AI | Gemini 2.5 Flash on Vertex AI |
| Listing connectors | Containerized services on Compute Engine, reached over private VPC egress |
| Frontend | Static HTML, CSS and vanilla JavaScript |

## Plans

| Plan | Searches per day |
|---|---|
| No account | 3 |
| Free | 20 |
| Pro / Team | 500 / 2,000 (contact sales) |

Details are on the [pricing page](https://tedrosai.com/pricing).

## Contact

Imran Alemar, Founder & Engineer · [tedros@tedrosai.com](mailto:tedros@tedrosai.com) · [tedrosai.com](https://tedrosai.com)
