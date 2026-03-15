# CORS Fix Instructions

## The Problem
When testing locally (http://127.0.0.1:9090), your browser blocks requests to n8n webhook because of CORS (Cross-Origin Resource Sharing) policy.

## Solution 1: Configure CORS in n8n (Recommended)

### Step 1: Update the Webhook Node
1. Open your n8n workflow
2. Click on the **"Webhook - Receive Quiz Data"** node
3. Scroll down to **"Options"** section
4. Enable **"CORS"** option
5. Set **"Allowed Origins"** to `*` (or specific origin like `http://127.0.0.1:9090`)

### Step 2: Update the Respond Node
1. Click on the **"Respond to Webhook"** node
2. Scroll down to **"Options"** section
3. Click **"Add Response Header"**
4. Add these headers:
   - **Name**: `Access-Control-Allow-Origin`, **Value**: `*`
   - **Name**: `Access-Control-Allow-Methods`, **Value**: `GET, POST, OPTIONS`
   - **Name**: `Access-Control-Allow-Headers`, **Value**: `Content-Type`

### Step 3: Re-import the Updated Workflow
The workflow JSON has been updated with CORS settings. You can:
- Re-import `n8n-workflow.json` (it will overwrite your current workflow)
- OR manually add the CORS settings as described above

## Solution 2: Use a Browser Extension (Quick Test)

For quick testing, you can use a CORS browser extension:
- **Chrome**: "CORS Unblock" or "Allow CORS"
- **Firefox**: "CORS Everywhere"

⚠️ **Note**: Only use extensions for testing, not for production!

## Solution 3: Deploy HTML to Same Domain

If you deploy your HTML to a public URL (like Netlify, Vercel, GitHub Pages), you can configure n8n to allow that specific origin instead of `*`.

## After Fixing CORS

1. **Save** the workflow in n8n
2. **Activate** the workflow (toggle switch ON)
3. **Hard refresh** your browser (Ctrl+F5)
4. **Test** the quiz again

The CORS error should be resolved!
