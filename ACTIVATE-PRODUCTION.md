# How to Activate Production Webhook in n8n

## The Problem
- Test webhook (`/webhook-test/`) works when manually executing
- Production webhook (`/webhook/`) is not active
- Email is sent but web response is not returned

## Solution: Activate Production Webhook

### Step 1: Activate the Workflow
1. Open your workflow in n8n
2. Look at the top of the workflow editor
3. Toggle the **"Active"** switch to **ON** (it should be green/active)
4. This activates the **Production** webhook

### Step 2: Get Production Webhook URL
1. Click on the **"Webhook - Receive Quiz Data"** node
2. You should now see **TWO URLs**:
   - **Test URL**: `https://clarisza.app.n8n.cloud/webhook-test/mbti-quiz` (for manual testing)
   - **Production URL**: `https://clarisza.app.n8n.cloud/webhook/mbti-quiz` (for live use)
3. Copy the **Production URL**

### Step 3: Update HTML File
1. Open `index.html`
2. Find line 567: `const webhookUrl = 'https://clarisza.app.n8n.cloud/webhook-test/mbti-quiz';`
3. Change it to: `const webhookUrl = 'https://clarisza.app.n8n.cloud/webhook/mbti-quiz';`
4. Save the file

### Step 4: Verify Webhook is Active
1. In n8n, the webhook node should show:
   - Status: **Active** or **Listening**
   - Production URL should be visible
2. If you see "Click to activate" or similar, click it

## Important Notes

### Test Mode vs Production Mode

**Test Mode (`/webhook-test/`):**
- Only works when you manually click "Execute Workflow" or "Test Workflow"
- Does NOT respond to external HTTP requests
- Used for debugging

**Production Mode (`/webhook/`):**
- Works automatically when workflow is **Active**
- Responds to external HTTP requests
- Required for your HTML page to work

### Why Email Works But Response Doesn't

- The workflow **executes** (that's why email is sent)
- But in test mode, the **webhook response** is not sent back to the browser
- Production mode is needed for the webhook to respond to your HTML page

## Troubleshooting

### If Production URL is not showing:
1. Make sure workflow is **Active** (toggle switch ON)
2. Click on webhook node
3. Click "Listen for Test Event" or "Activate" button
4. Production URL should appear

### If Production URL shows but doesn't work:
1. Check that workflow is **Active**
2. Try accessing the URL directly in browser (should show n8n webhook page)
3. Check n8n execution logs to see if requests are received
4. Verify CORS is enabled in webhook node options

### If you still get "No MBTI result received":
1. Check n8n execution logs
2. Look at "Respond to Webhook" node output
3. Verify it's sending: `{ "mbtiResult": "..." }`
4. Check browser console (F12) → Network tab → see the actual response

## Quick Checklist

- [ ] Workflow is **Active** (toggle switch ON)
- [ ] Production webhook URL is visible
- [ ] HTML file uses `/webhook/` not `/webhook-test/`
- [ ] CORS is enabled in webhook node
- [ ] "Respond to Webhook" node is connected
- [ ] Test the production URL directly in browser
