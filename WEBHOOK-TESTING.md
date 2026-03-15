# Webhook Testing Guide

## Understanding the Error

**Error:** `"This webhook is not registered for GET requests. Did you mean to make a POST request?"`

### Why This Happens

- **Opening the URL in a browser** = GET request ❌ (This will always show the error)
- **Your HTML page submitting the form** = POST request ✅ (This should work)

The error is **NORMAL** when you open the webhook URL directly in a browser. The webhook is configured for POST requests only, which is correct.

## How to Test Properly

### ✅ Method 1: Test from Your HTML Page (Recommended)

1. Open your HTML page: `http://localhost:9090/index.html`
2. Answer all 8 questions
3. Enter your email
4. Click "Submit Quiz"
5. Check if the result appears on the page

**This is the REAL test** - if this works, your webhook is working correctly!

### ✅ Method 2: Test with Browser DevTools

1. Open your HTML page
2. Press **F12** to open DevTools
3. Go to **Network** tab
4. Submit the quiz
5. Look for the POST request to `/webhook/mbti-quiz`
6. Click on it and check:
   - **Status**: Should be `200 OK` (not 404)
   - **Response**: Should show `{"mbtiResult": "..."}`

### ✅ Method 3: Test with curl (Command Line)

```bash
curl -X POST https://clarisza.app.n8n.cloud/webhook/mbti-quiz \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test@example.com",
    "answers": {
      "1": "introvert",
      "2": "thinking",
      "3": "sensing",
      "4": "judging",
      "5": "introvert",
      "6": "thinking",
      "7": "sensing",
      "8": "judging"
    }
  }'
```

**Expected Response:**
```json
{
  "mbtiResult": "MBTI Type: ISTJ\nDescription: ..."
}
```

### ❌ What NOT to Do

**Don't test by opening the URL in a browser:**
- `https://clarisza.app.n8n.cloud/webhook/mbti-quiz`
- This will ALWAYS show the GET error (this is normal!)

## Checking if Webhook is Working

### In n8n:

1. **Check Workflow Status:**
   - Workflow should be **Active** (toggle switch ON)
   - Webhook node should show "Listening" or "Active"

2. **Check Executions:**
   - Go to **Executions** tab
   - You should see new executions when you submit the quiz
   - Click on an execution to see the flow

3. **Check Webhook Node:**
   - Click "Webhook - Receive Quiz Data" node
   - Production URL should be visible
   - Should show: `https://clarisza.app.n8n.cloud/webhook/mbti-quiz`

### In Browser Console:

1. Open DevTools (F12)
2. Go to **Console** tab
3. Submit the quiz
4. Look for:
   - ✅ Success: No errors, result appears
   - ❌ Error: Check the error message

### Common Issues

**Issue: Still getting "No MBTI result received"**
- Check n8n execution logs
- Verify "Respond to Webhook" node is executing
- Check if OpenAI API call is successful

**Issue: CORS error in console**
- Make sure CORS is enabled in webhook node options
- Check response headers include CORS headers

**Issue: 404 error when submitting**
- Verify workflow is **Active**
- Check webhook URL is correct (should be `/webhook/` not `/webhook-test/`)
- Make sure you're using Production URL, not Test URL

## Quick Verification Checklist

- [ ] Workflow is **Active** in n8n
- [ ] Webhook node shows Production URL
- [ ] HTML file uses `/webhook/` URL (not `/webhook-test/`)
- [ ] Test by submitting quiz from HTML page (not opening URL in browser)
- [ ] Check browser Network tab for POST request
- [ ] Check n8n Executions tab for new executions
- [ ] Verify "Respond to Webhook" node executes successfully

## Summary

**The GET error is NORMAL and EXPECTED** when opening the webhook URL in a browser. 

**The real test** is submitting the quiz from your HTML page. If that works, your webhook is configured correctly!
