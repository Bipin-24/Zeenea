# Algolia DocSearch Setup Guide

This guide will help you configure Algolia DocSearch for your documentation portal.

## Current Status ✅

Your project already has:
- ✅ `@docsearch/react` package installed (v3.6.0)
- ✅ `@docsearch/css` imported in `_app.js`
- ✅ Search component configured in `TopNav.js`
- ✅ `.env.local` file created (needs your credentials)
- ✅ Algolia preconnect configured in `_app.js`
- ✅ Algolia site verification meta tag added (`50A0B4A451D65BAB`)

## Step 1: Get Algolia Credentials

You have two options:

### Option A: Apply for Free DocSearch (Recommended for Open Source)

1. Visit https://docsearch.algolia.com/apply/
2. Fill out the application form with:
   - Your documentation URL
   - Repository URL (if open source)
   - Email address
3. Wait for approval (usually within a few days)
4. Once approved, you'll receive an email with:
   - `appId`
   - `apiKey`
   - `indexName`

### Option B: Create Your Own Algolia Account

1. Sign up at https://www.algolia.com/
2. Create a new application
3. Create a search index for your docs
4. Set up a crawler to index your documentation
5. Get your credentials from the dashboard

## Step 2: Update Environment Variables

Open `.env.local` and replace the placeholder values:

```env
NEXT_PUBLIC_ALGOLIA_APP_ID=YOUR_ACTUAL_APP_ID
NEXT_PUBLIC_ALGOLIA_API_KEY=YOUR_ACTUAL_SEARCH_API_KEY
```

**Important Notes:**
- Use the **Search-Only API Key** (not the Admin API Key)
- The Search API Key is safe to expose in frontend code
- Never commit `.env.local` to Git (it's already in `.gitignore`)

## Step 3: Update Index Name (if needed)

If your Algolia index name is different from "markdoc", update it in `components/Shell/TopNav.js`:

```javascript
<DocSearch
  appId={process.env.NEXT_PUBLIC_ALGOLIA_APP_ID}
  apiKey={process.env.NEXT_PUBLIC_ALGOLIA_API_KEY}
  indexName="YOUR_INDEX_NAME" // Change this
/>
```

## Step 4: Restart Development Server

After updating `.env.local`, restart your dev server:

```bash
# Stop the current server (Ctrl+C)
npm run dev
```

## Step 5: Test the Search

1. Open your browser to http://localhost:3001
2. Click the search button in the top navigation
3. Try searching for content from your documentation
4. If nothing appears, your index might need time to be populated

## Troubleshooting

### Search button appears but no results

- **Cause**: Index is empty or not yet crawled
- **Solution**: Wait for Algolia to crawl your site, or manually configure the crawler

### "Invalid credentials" error

- **Cause**: Wrong App ID or API Key
- **Solution**: Double-check your `.env.local` values

### Search modal doesn't open

- **Cause**: JavaScript error or missing dependencies
- **Solution**: Check browser console for errors, ensure all packages are installed

## Configure Algolia Crawler (For Custom Setup)

If you're using your own Algolia account, you'll need to configure the crawler:

1. Go to Algolia Dashboard → Crawler
2. Add a new crawler for your documentation site
3. Configure the crawler settings:
   ```json
   {
     "index_name": "your_index_name",
     "start_urls": ["https://your-docs-site.com/"],
     "selectors": {
       "lvl0": "h1",
       "lvl1": "h2",
       "lvl2": "h3",
       "lvl3": "h4",
       "text": "p, li"
     }
   }
   ```

## Current Configuration Files

### TopNav.js (Already Configured)
```javascript
import { DocSearch } from '@docsearch/react';

function Search() {
  return (
    <DocSearch
      appId={process.env.NEXT_PUBLIC_ALGOLIA_APP_ID}
      apiKey={process.env.NEXT_PUBLIC_ALGOLIA_API_KEY}
      indexName="markdoc"
    />
  );
}
```

### _app.js (Already Configured)
- DocSearch CSS imported: `import '@docsearch/css';`
- Algolia preconnect configured for faster loading

## Next Steps

1. Get your Algolia credentials (Option A or B above)
2. Update `.env.local` with your actual values
3. Update `indexName` in TopNav.js if needed
4. Restart your dev server
5. Test the search functionality

## Need Help?

- Algolia DocSearch Docs: https://docsearch.algolia.com/docs/what-is-docsearch
- Algolia Community: https://discourse.algolia.com/
- DocSearch GitHub: https://github.com/algolia/docsearch

---

**Note**: The search will only work once:
1. You have valid Algolia credentials in `.env.local`
2. Your documentation has been indexed by Algolia
3. The dev server has been restarted after updating `.env.local`
