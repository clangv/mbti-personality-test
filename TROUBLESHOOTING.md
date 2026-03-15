# Troubleshooting Guide

## Issue: "No MBTI result received" Error

### Problem
The web page shows "No MBTI result received" even though the email is delivered.

### Solutions

#### 1. Fix Webhook URL (Already Fixed)
✅ The HTML file has been updated to use `/webhook/` instead of `/webhook-test/`

**Verify in n8n:**
- Go to your workflow
- Click "Webhook - Receive Quiz Data" node
- Check the **Production URL** - it should be: `https://clarisza.app.n8n.cloud/webhook/mbti-quiz`
- Make sure the workflow is **Active**

#### 2. Check OpenAI API Response

The workflow might be failing at the OpenAI API call. Check:

1. **Open n8n execution logs:**
   - Go to "Executions" tab
   - Click on the latest execution
   - Check each node's output

2. **Verify OpenAI API credentials:**
   - Click "AI Analysis - OpenAI" node
   - Check credentials are configured
   - The Authorization header should use: `Bearer YOUR_API_KEY`
   - Make sure you have API credits

3. **Check API response format:**
   - In the execution, click "AI Analysis - OpenAI" node
   - Check the output - it should have a `body` field with JSON
   - The JSON should have: `choices[0].message.content`

#### 3. Fix Authorization Header

If the OpenAI API call is failing, update the Authorization header:

1. Click "AI Analysis - OpenAI" node
2. In **Header Parameters**, find the Authorization header
3. The value should be: `Bearer YOUR_API_KEY`
   - If using HTTP Header Auth credential: `Bearer {{ $credentials.httpHeaderAuth.value }}`
   - Or manually: `Bearer sk-...your-key...`

#### 4. Test the Workflow Manually

1. In n8n, click "Execute Workflow" button
2. Manually trigger with test data:
   ```json
   {
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
   }
   ```
3. Check each node's output to see where it fails

#### 5. Check Response Node

Verify the "Respond to Webhook" node:
1. Click "Respond to Webhook" node
2. Check **Response Body** field
3. It should be: `{{ { "mbtiResult": $json.mbtiResult } }}`
4. Make sure it's connected to "Format Result" node

#### 6. Debug Steps

**Step 1: Check if webhook receives data**
- Look at "Extract Data" node output in execution
- Should show email and answers

**Step 2: Check if OpenAI responds**
- Look at "AI Analysis - OpenAI" node output
- Should show API response with choices array

**Step 3: Check if result is formatted**
- Look at "Format Result" node output
- Should show mbtiResult, email, and answers

**Step 4: Check if response is sent**
- Look at "Respond to Webhook" node output
- Should show the response that was sent

### Common Issues

**Issue: OpenAI API returns error**
- **Solution**: Check API key, credits, and model name (try `gpt-3.5-turbo` instead of `gpt-4`)

**Issue: Response parsing fails**
- **Solution**: Check the "Format Result" node - the expression `JSON.parse($json.body).choices[0].message.content` might need adjustment

**Issue: Webhook timeout**
- **Solution**: OpenAI API calls can take time. Make sure n8n timeout settings allow enough time (30+ seconds)

**Issue: CORS still blocking**
- **Solution**: Make sure CORS is enabled in webhook node and response headers are set

### Quick Test

1. **Test webhook directly:**
   - Open: `https://clarisza.app.n8n.cloud/webhook/mbti-quiz`
   - Should show n8n webhook page (not 404)

2. **Test with curl:**
   ```bash
   curl -X POST https://clarisza.app.n8n.cloud/webhook/mbti-quiz \
     -H "Content-Type: application/json" \
     -d '{"email":"test@test.com","answers":{"1":"introvert","2":"thinking","3":"sensing","4":"judging","5":"introvert","6":"thinking","7":"sensing","8":"judging"}}'
   ```
   - Should return JSON with `mbtiResult`

3. **Check browser console:**
   - Open browser DevTools (F12)
   - Go to Network tab
   - Submit quiz
   - Check the POST request to webhook
   - Look at Response tab - should show JSON with mbtiResult

### Still Not Working?

1. **Check n8n version** - Some older versions handle responses differently
2. **Try using n8n's OpenAI node** instead of HTTP Request (if available)
3. **Add error handling** - Add an IF node to check if mbtiResult exists before responding
4. **Check execution mode** - Make sure workflow is set to "Production" not "Test"
