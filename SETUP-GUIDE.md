# Quick Setup Guide

## Step-by-Step Setup

### 1. Import n8n Workflow

1. Open your n8n instance
2. Go to **Workflows** → Click **Import from File**
3. Select `n8n-workflow.json`

### 2. Configure OpenAI API

**Option A: Using HTTP Header Auth (Recommended)**
1. Click on the **"AI Analysis - OpenAI"** node
2. In the **Authentication** dropdown, select **"Generic Credential Type"**
3. Select **"HTTP Header Auth"**
4. Click **"Create New Credential"**
5. Set:
   - **Name**: `Authorization`
   - **Value**: `Bearer sk-your-openai-api-key-here`
   - Replace `sk-your-openai-api-key-here` with your actual OpenAI API key

**Option B: Using n8n's OpenAI Node (if available)**
- If your n8n instance has the OpenAI node installed, you can replace the HTTP Request node with it
- Configure it with your API key in the credentials section

### 3. Configure Email (SMTP)

1. Click on the **"Send Email"** node
2. Click on **Credentials** → **Create New**
3. Select **SMTP**
4. Fill in your email provider details:

   **For Gmail:**
   - User: `your-email@gmail.com`
   - Password: Use an [App Password](https://support.google.com/accounts/answer/185833)
   - Host: `smtp.gmail.com`
   - Port: `587`
   - Secure: `TLS`

   **For Outlook:**
   - User: `your-email@outlook.com`
   - Password: Your password
   - Host: `smtp-mail.outlook.com`
   - Port: `587`
   - Secure: `TLS`

5. Update the **"fromEmail"** field in the node to match your email

### 4. Get Webhook URL

1. Click on **"Webhook - Receive Quiz Data"** node
2. Click **"Listen for Test Event"** or toggle the workflow to **Active**
3. Copy the **Production URL** that appears
4. It will look like: `https://your-n8n-instance.com/webhook/mbti-quiz`

### 5. Update HTML File

1. Open `index.html` in a text editor
2. Find line ~340: `const webhookUrl = 'https://clarisza.app.n8n.cloud/webhook-test/mbti-quiz';`
3. Replace with your webhook URL:
   ```javascript
   const webhookUrl = 'https://clarisza.app.n8n.cloud/webhook-test/mbti-quiz';
   ```

### 6. Activate Workflow

1. Toggle the **Active** switch at the top of the workflow
2. The workflow is now ready!

### 7. Test

1. Open `index.html` in a web browser
2. Answer all 8 questions
3. Enter your email
4. Click **Submit Quiz**
5. You should see your MBTI result and receive an email

## Troubleshooting

**"Network response was not ok" error:**
- Check that the webhook URL is correct
- Ensure the workflow is activated
- Check n8n execution logs for errors

**OpenAI API errors:**
- Verify your API key is correct
- Check you have API credits
- Try changing model to `gpt-3.5-turbo` if `gpt-4` is unavailable

**Email not sending:**
- Verify SMTP credentials
- Check spam folder
- For Gmail, ensure "Less secure app access" is enabled or use App Password

**CORS errors:**
- If hosting HTML on a different domain, you may need to configure CORS in n8n
- Or use a proxy/server to serve the HTML from the same domain

## Alternative: Using n8n Cloud

If using n8n.cloud:
1. The webhook URL will be: `https://your-workflow-name.app.n8n.cloud/webhook/mbti-quiz`
2. All other steps remain the same
