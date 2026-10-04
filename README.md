# Drupal + Next.js Gallery API — Today's APOD Custom Module

Full-stack implementation of the NASA Astronomy Picture of the Day (APOD) feature, built for a headless Drupal 11 + Next.js architecture. This repo contains both the Drupal backend module and the Next.js frontend component together.

## Structure

```
drupal/       Drupal 11 custom module (francisco_apod)
frontend/     Next.js component and API proxy route
```

---

## Drupal module (`drupal/`)

Provides the `apod` paragraph type and a JSON config endpoint for the frontend.

**What it does:**
- Registers an `apod` paragraph type so editors can place the component on any landing page via the CMS
- Exposes `/api/apod/config` — a JSON endpoint the Next.js frontend reads for the NASA API key, gallery count, and cache duration
- Admin settings form at `/admin/config/francisco/apod` to control all values without touching code
- API key is stored securely in Drupal config, never hardcoded or exposed to the browser

**Admin settings:**

| Setting | Description | Default |
|---------|-------------|---------|
| NASA API Key | Free key at api.nasa.gov. Use `DEMO_KEY` for testing | `DEMO_KEY` |
| Gallery image count | Number of random APOD images in the gallery row | `6` |
| Cache duration | Next.js ISR cache TTL in seconds | `3600` |

**Installation:**
```bash
cp -r drupal /path/to/web/modules/custom/francisco_apod
drush en francisco_apod -y
drush cr
```

Requires Drupal 10 or 11 with the `paragraphs` module.

---

## Next.js frontend (`frontend/`)

**What it does:**
- Fetches and displays today's APOD (image or video) on load
- Shows a random gallery of past APOD entries as clickable thumbnails
- Clicking a thumbnail swaps the main panel to that entry
- Refresh button loads a new random gallery set
- "Back to today" link appears when viewing a past entry

**File breakdown:**

| File | Purpose |
|------|---------|
| `src/app/api/apod/route.ts` | Next.js API proxy — reads config from Drupal, fetches from NASA |
| `src/components/paragraphs/apod/ApodDisplay.tsx` | Main React component |
| `src/components/paragraphs/apod/types.ts` | TypeScript interfaces |
| `src/components/paragraphs/apod/index.ts` | Barrel export |

**API route modes:**

| `?mode=` | Returns |
|----------|---------|
| `today` | Single most recent APOD entry |
| `random` | Multiple entries (count from Drupal config) |

---

## Stack

- Drupal 11 (headless, Pantheon)
- PHP 8.2
- Next.js 16 App Router (Vercel)
- TypeScript
- NASA public API (no key required for `DEMO_KEY` testing)
