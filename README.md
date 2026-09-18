# SONARIC Product Scraper

A Python web scraper that extracts the full product catalog from [sonaric.dz](https://www.sonaric.dz/), an Algerian home appliances manufacturer (cooking, water heaters, heating, and air conditioning).

## What it does

1. Discovers all product category links from the site's navigation menu
2. Visits each category page and collects individual product links (deduplicated by product ID, since some products appear under multiple categories, e.g. "Nouveautés")
3. Visits each product page and extracts:
   - Product name
   - Full "Caractéristiques" table (model, dimensions, power, materials, etc. — varies by product type)
4. Saves everything to `sonaric_produits.csv`

## Tech stack

- `requests` — HTTP requests
- `BeautifulSoup` — HTML parsing
- `pandas` — table extraction (`read_html`) and CSV export
- `re` / `urllib.parse.urljoin` — robust link handling

## Key challenges solved

- **Hidden navigation**: category links are nested inside a collapsed dropdown `<div>`, not directly accessible from top-level menu items — required inspecting the DOM structure via browser DevTools.
- **Inconsistent relative URLs**: product links use different relative path styles depending on the page; handled with `urljoin()` instead of manual string concatenation.
- **Cross-category duplicates**: the same product can appear under its main category *and* under "Nouveautés" with a different URL path — deduplicated using the product's numeric ID rather than the full URL string.
- **Messy table parsing**: the characteristics table includes a duplicated header row and single-value "badge" rows (e.g. "Allumage électrique") that `pandas.read_html()` parses inconsistently — cleaned during extraction.

## Usage

```bash
pip install requests beautifulsoup4 pandas lxml
python sonaric_scraper.py
```

Output: `sonaric_produits.csv` — one row per product, with dynamic columns depending on product type (climatiseur, chauffe-bain, table de cuisson, etc.).

## Ethics

This scraper targets publicly available product information only, includes a polite 1-second delay between requests, and identifies itself with a standard browser User-Agent.

---
*Built as part of a Data Analyst portfolio — Feriel*
