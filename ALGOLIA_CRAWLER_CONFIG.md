# Algolia Crawler Configuration for DocSearch

## Step-by-Step: Fix Your Index Structure

### Problem
Your index `zdoc_netlify_app_klx2xamsju_pages` returns records without the `hierarchy` structure that DocSearch requires.

### Solution: Configure Algolia Crawler with DocSearch Helper

1. **Login to Algolia Dashboard**
   - Go to: https://www.algolia.com/account/
   - Navigate to your application with App ID: `KLX2XAMSJU`

2. **Go to Crawler Settings**
   - Click on "Data Sources" → "Crawler"
   - Find your crawler for `zdoc_netlify_app_klx2xamsju_pages`
   - Click "Edit" or "Configuration"

3. **Replace the Record Extractor with This Code:**

```javascript
new Crawler({
  appId: "KLX2XAMSJU",
  apiKey: "YOUR_ADMIN_API_KEY",
  rateLimit: 8,
  startUrls: ["https://zdoc.netlify.app/"],
  sitemaps: ["https://zdoc.netlify.app/sitemap.xml"],
  ignoreCanonicalTo: false,
  discoveryPatterns: ["https://zdoc.netlify.app/**"],
  schedule: "at 05:00 every 1 day",
  actions: [
    {
      indexName: "zdoc_netlify_app_klx2xamsju_pages",
      pathsToMatch: ["https://zdoc.netlify.app/**"],
      recordExtractor: ({ $, helpers }) => {
        // Use the DocSearch helper to extract properly formatted records
        return helpers.docsearch({
          recordProps: {
            lvl0: {
              selectors: "",
              defaultValue: "Documentation"
            },
            lvl1: ["header h1", "article h1", "main h1", "h1"],
            lvl2: ["article h2", "main h2", "h2"],
            lvl3: ["article h3", "main h3", "h3"],
            lvl4: ["article h4", "main h4", "h4"],
            lvl5: ["article h5", "main h5", "h5"],
            content: ["article p", "article li", "main p", "main li", "p", "li"],
            tags: {
              defaultValue: ["docs"]
            }
          },
          indexHeadings: true,
          aggregateContent: true,
          recordVersion: "v3"
        });
      }
    }
  ],
  initialIndexSettings: {
    zdoc_netlify_app_klx2xamsju_pages: {
      attributesForFaceting: ["type", "lang"],
      attributesToRetrieve: ["hierarchy", "content", "anchor", "url"],
      attributesToHighlight: ["hierarchy", "content"],
      attributesToSnippet: ["content:10"],
      camelCaseAttributes: ["hierarchy", "content"],
      searchableAttributes: [
        "unordered(hierarchy.lvl0)",
        "unordered(hierarchy.lvl1)",
        "unordered(hierarchy.lvl2)",
        "unordered(hierarchy.lvl3)",
        "unordered(hierarchy.lvl4)",
        "unordered(hierarchy.lvl5)",
        "content"
      ],
      distinct: true,
      attributeForDistinct: "url",
      customRanking: ["desc(weight.pageRank)", "desc(weight.level)", "asc(weight.position)"],
      ranking: ["words", "filters", "typo", "attribute", "proximity", "exact", "custom"],
      highlightPreTag: '<span class="algolia-docsearch-suggestion--highlight">',
      highlightPostTag: "</span>",
      minWordSizefor1Typo: 3,
      minWordSizefor2Typos: 7,
      allowTyposOnNumericTokens: false,
      minProximity: 1,
      ignorePlurals: true,
      advancedSyntax: true,
      attributeCriteriaComputedByMinProximity: true,
      removeWordsIfNoResults: "allOptional"
    }
  }
});
```

4. **Save and Run Crawler**
   - Click "Save"
   - Click "Restart crawling" or "Run crawler"
   - Wait 5-10 minutes for the crawl to complete

5. **Verify Index Structure**
   - Go to "Search" → "Index" → `zdoc_netlify_app_klx2xamsju_pages`
   - Click "Browse"
   - Check that records now have this structure:
   ```json
   {
     "objectID": "...",
     "hierarchy": {
       "lvl0": "Documentation",
       "lvl1": "Getting Started",
       "lvl2": "Installation",
       ...
     },
     "content": "...",
     "url": "..."
   }
   ```

6. **Test Search**
   - Once crawler completes, your search should work!
   - Try it at: http://localhost:3001

## Simpler Alternative: Use Algolia's Web UI

If the above JSON is too complex, use Algolia's visual crawler editor:

1. Go to Algolia Dashboard → Crawler
2. Select your crawler
3. Click "Actions" → "Edit with visual editor"
4. Set these options:
   - **Record extractor**: DocSearch
   - **Content selectors**:
     - h1: Main headings
     - h2: Subheadings
     - h3-h5: Nested headings
     - p, li: Content
5. Save and run crawler

## Quick Test

After crawler runs, test if index is fixed:

```bash
# Check if records have hierarchy
curl -X POST \
  -H "X-Algolia-Application-Id: KLX2XAMSJU" \
  -H "X-Algolia-API-Key: f2e6744793d715b4e08d4105e0f5b594" \
  "https://KLX2XAMSJU-dsn.algolia.net/1/indexes/zdoc_netlify_app_klx2xamsju_pages/query" \
  -d '{"query":""}' | grep -o "hierarchy"
```

If you see "hierarchy" in the output, your index is properly configured!

---

## Why This Happens

- **markdocs_test works** because their Algolia index (`3ZH45TUUP7`) is properly configured with DocSearch structure
- **Your index doesn't work** because it wasn't configured with the DocSearch helper
- **DocSearch requires** all records to have `hierarchy.lvl0` and `hierarchy.lvl1` at minimum

## Need Help?

If you can't access the Algolia crawler settings, you may need:
- Admin access to the Algolia account
- Or, create a new Algolia app and configure it properly from the start
- Or, temporarily disable search until index is fixed

---

**Bottom line**: The code is correct - the issue is with the Algolia index configuration. Once you configure the crawler with the DocSearch helper, search will work perfectly! 🎯
