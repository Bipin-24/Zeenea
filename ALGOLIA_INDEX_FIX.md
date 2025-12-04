# Algolia Index Structure Issue

## Problem

Your Algolia index `zdoc_netlify_app_klx2xamsju_pages` doesn't have the hierarchical structure that DocSearch expects. DocSearch requires records to have a specific `hierarchy` structure:

```json
{
  "objectID": "123",
  "url": "https://example.com/page",
  "hierarchy": {
    "lvl0": "Documentation",
    "lvl1": "Getting Started",
    "lvl2": "Installation",
    "lvl3": null,
    "lvl4": null,
    "lvl5": null,
    "lvl6": null
  },
  "content": "Page content here..."
}
```

## Solution Options

### Option 1: Reconfigure Your Algolia Crawler (Recommended)

You need to configure your Algolia crawler to extract the proper hierarchy from your pages.

1. Go to Algolia Dashboard → Data Sources → Crawler
2. Edit your crawler configuration
3. Use this record extractor:

```javascript
({
  helpers,
  $,
  url,
}) => {
  return helpers.docsearch({
    recordProps: {
      lvl0: {
        selectors: '',
        defaultValue: 'Documentation',
      },
      lvl1: 'h1',
      lvl2: 'h2',
      lvl3: 'h3',
      lvl4: 'h4',
      lvl5: 'h5',
      content: 'p, li, td',
    },
    indexHeadings: true,
    aggregateContent: true,
  });
};
```

4. Save and run a new crawl
5. Wait for crawl to complete (this will recreate your index with proper structure)

### Option 2: Use Algolia InstantSearch Instead of DocSearch

If you can't modify the crawler, we can use regular Algolia InstantSearch which is more flexible:

```bash
npm install react-instantsearch-dom
```

Then replace the Search component with a custom implementation that works with any index structure.

### Option 3: Temporarily Disable Search

Until you fix the index structure, you can comment out the search component to prevent errors.

## Current Status

- ❌ DocSearch is failing because of missing `hierarchy.lvl1` field
- ✅ Environment variables are configured correctly
- ✅ Algolia verification meta tag is added
- ⏳ Index structure needs to be fixed

## Next Steps

1. **Reconfigure the crawler** with the docsearch helper (see Option 1)
2. **Wait for reindex** (usually 5-10 minutes)
3. **Test search** - it should work once the index has proper structure

---

**Note**: The error `Cannot read properties of undefined (reading 'lvl1')` occurs because DocSearch expects ALL records to have a `hierarchy` object with at least `lvl0` and `lvl1` fields. Your current index records don't have this structure.
