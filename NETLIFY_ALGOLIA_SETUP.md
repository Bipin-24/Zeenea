# Netlify + Algolia Setup Guide

This guide will help you configure Algolia search on your Netlify-deployed documentation portal.

## Your Algolia Credentials ✅

```
App ID:     KLX2XAMSJU
API Key:    f2e6744793d715b4e08d4105e0f5b594
Index Name: zdoc_netlify_app_klx2xamsju_pages
```

## Step 1: Configure Environment Variables on Netlify

### Via Netlify Dashboard (Recommended)

1. **Login to Netlify**:
   - Go to https://app.netlify.com/
   - Select your site (tirthanportal)

2. **Add Environment Variables**:
   - Click **Site settings** (top navigation)
   - Click **Environment variables** (left sidebar under "Build & deploy")
   - Click **Add a variable**

3. **Add these three variables**:

   **Variable 1:**
   - Key: `NEXT_PUBLIC_ALGOLIA_APP_ID`
   - Value: `KLX2XAMSJU`
   - Scopes: `All` (or select specific scopes)
   - Click **Create variable**

   **Variable 2:**
   - Key: `NEXT_PUBLIC_ALGOLIA_API_KEY`
   - Value: `f2e6744793d715b4e08d4105e0f5b594`
   - Scopes: `All`
   - Click **Create variable**

   **Variable 3:**
   - Key: `NEXT_PUBLIC_ALGOLIA_INDEX_NAME`
   - Value: `zdoc_netlify_app_klx2xamsju_pages`
   - Scopes: `All`
   - Click **Create variable**

4. **Trigger New Deploy**:
   - Go to **Deploys** tab
   - Click **Trigger deploy** → **Deploy site**
   - Wait for deployment to complete (~2-3 minutes)

### Via Netlify CLI (Alternative)

If you have Netlify CLI installed:

```bash
netlify env:set NEXT_PUBLIC_ALGOLIA_APP_ID "KLX2XAMSJU"
netlify env:set NEXT_PUBLIC_ALGOLIA_API_KEY "f2e6744793d715b4e08d4105e0f5b594"
netlify env:set NEXT_PUBLIC_ALGOLIA_INDEX_NAME "zdoc_netlify_app_klx2xamsju_pages"
```

Then trigger a deploy:
```bash
netlify deploy --prod
```

## Step 2: Verify Local Setup

Your local `.env.local` has been updated with the correct credentials. To test locally:

```bash
# Restart your dev server
npm run dev
```

Visit http://localhost:3001 and try the search functionality.

## Step 3: Configure Algolia Crawler

Now you need to set up the crawler to index your documentation:

### 3.1 Access Algolia Dashboard

1. Go to https://www.algolia.com/account/
2. Navigate to your application
3. Select the index: `zdoc_netlify_app_klx2xamsju_pages`

### 3.2 Configure Crawler

1. Go to **Data sources** → **Crawler**
2. Click **New Crawler**
3. Configure with these settings:

**Basic Configuration:**
```json
{
  "indexName": "zdoc_netlify_app_klx2xamsju_pages",
  "pathsToMatch": ["https://tirthanportal.netlify.app/**"],
  "recordExtractor": ({ url, $, helpers }) => {
    return helpers.docsearch({
      recordProps: {
        lvl0: "Documentation",
        lvl1: "h1",
        lvl2: "h2",
        lvl3: "h3",
        lvl4: "h4",
        lvl5: "h5",
        content: "p, li"
      },
      indexHeadings: true
    });
  }
}
```

**Schedule:** Set to run daily or weekly

### 3.3 Start Initial Crawl

1. Click **Save & Start Crawling**
2. Monitor the crawl progress
3. Once complete, your search will have content!

## Step 4: Test the Search

1. **On Netlify (Production)**:
   - Visit https://tirthanportal.netlify.app/
   - Click the search icon in top navigation
   - Search should now work with indexed content

2. **Locally (Development)**:
   - Run `npm run dev`
   - Visit http://localhost:3001
   - Search will use the same index as production

## Troubleshooting

### "No results found"
- **Cause**: Index is empty or crawler hasn't run yet
- **Solution**: 
  - Check Algolia dashboard to see if crawler has completed
  - Manually trigger a crawl
  - Wait 5-10 minutes for initial indexing

### Search button doesn't appear
- **Cause**: Environment variables not loaded
- **Solution**:
  - Verify env vars are set in Netlify dashboard
  - Trigger a new deploy
  - Check build logs for errors

### "Invalid credentials" error
- **Cause**: Wrong API key or App ID
- **Solution**:
  - Double-check env vars in Netlify dashboard
  - Ensure no extra spaces in values
  - Redeploy after fixing

### Search works locally but not on Netlify
- **Cause**: Environment variables not configured on Netlify
- **Solution**: Follow Step 1 again to add env vars

## Verification Checklist

✅ Local `.env.local` updated with credentials  
✅ `TopNav.js` updated with correct index name  
✅ Algolia verification meta tag added to site  
✅ Environment variables added to Netlify  
✅ Site redeployed on Netlify  
✅ Algolia crawler configured  
✅ Initial crawl completed  
✅ Search tested and working  

## Next Steps

1. ✅ Add environment variables to Netlify (see Step 1)
2. ✅ Trigger new deploy on Netlify
3. ⏳ Configure Algolia crawler (see Step 3)
4. ⏳ Wait for initial crawl to complete
5. ⏳ Test search functionality

## Need Help?

- **Netlify Environment Variables**: https://docs.netlify.com/environment-variables/overview/
- **Algolia Crawler**: https://www.algolia.com/doc/tools/crawler/getting-started/overview/
- **DocSearch Configuration**: https://docsearch.algolia.com/docs/record-extractor/

---

**Important**: After adding environment variables to Netlify, always trigger a new deploy for changes to take effect!
