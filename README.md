# MBTI Personality Quiz - n8n Integration

This project contains a complete MBTI personality quiz system with n8n workflow integration.

## Files

- `index.html` - The quiz interface with 8 image-based questions
- `n8n-workflow.json` - The n8n workflow that processes quiz answers and generates MBTI results

## Setup Instructions

### 1. HTML Page Setup

1. Open `index.html` in a text editor
2. Find the line: `const webhookUrl = 'YOUR_N8N_WEBHOOK_URL_HERE';`
3. Replace `YOUR_N8N_WEBHOOK_URL_HERE` with your actual n8n webhook URL (you'll get this after setting up the workflow)

### 2. n8n Workflow Setup

#### Step 1: Import the Workflow

1. Open your n8n instance
2. Click on "Workflows" in the sidebar
3. Click the "..." menu and select "Import from File"
4. Select the `n8n-workflow.json` file

#### Step 2: Configure Credentials

You'll need to set up two credentials:

**A. OpenAI API Credentials (HTTP Header Auth)**
1. In the "AI Analysis - OpenAI" node, click on "Credentials"
2. Create a new HTTP Header Auth credential
3. Set:
   - **Name**: `Authorization`
   - **Value**: `Bearer YOUR_OPENAI_API_KEY`
   - Replace `YOUR_OPENAI_API_KEY` with your actual OpenAI API key

**B. SMTP Email Credentials**
1. In the "Send Email" node, click on "Credentials"
2. Create a new SMTP credential
3. Configure with your email provider settings:
   - **User**: Your email address
   - **Password**: Your email password or app-specific password
   - **Host**: Your SMTP server (e.g., `smtp.gmail.com` for Gmail)
   - **Port**: Your SMTP port (e.g., `587` for Gmail)
   - **Secure**: TLS/SSL (depending on your provider)

#### Step 3: Update Email Settings

1. In the "Send Email" node, update the `fromEmail` field with your actual email address
2. Make sure it matches the email used in your SMTP credentials

#### Step 4: Activate the Webhook

1. Click on the "Webhook - Receive Quiz Data" node
2. Click "Listen for Test Event" or "Execute Workflow" to activate it
3. Copy the webhook URL that appears
4. Paste this URL into the `index.html` file (replace `YOUR_N8N_WEBHOOK_URL_HERE`)

#### Step 5: Activate the Workflow

1. Toggle the "Active" switch at the top of the workflow
2. The workflow is now ready to receive quiz submissions

### 3. Deploy the HTML Page

You can deploy the HTML page in several ways:

**Option A: Local Testing**
- Simply open `index.html` in a web browser
- Note: Some browsers may block local file requests to external APIs

**Option B: Simple HTTP Server**
```bash
# Using Python
python -m http.server 8000

# Using Node.js (if you have http-server installed)
npx http-server -p 8000
```

**Option C: Deploy to Web Hosting**
- Upload `index.html` to any web hosting service (GitHub Pages, Netlify, Vercel, etc.)

## How It Works

1. **User takes the quiz**: The user answers 8 questions by selecting images
2. **Data submission**: Answers and email are sent to the n8n webhook
3. **AI analysis**: The workflow sends answers to OpenAI for MBTI analysis
4. **Result returned**: The MBTI result is sent back to the HTML page and displayed
5. **Email sent**: An email with the results and answers is sent to the user

## Workflow Node Details

- **Webhook - Receive Quiz Data**: Receives POST requests from the HTML page
- **Extract Data**: Extracts email, answers, and timestamp from the request
- **AI Analysis - OpenAI**: Sends answers to OpenAI GPT-4 for MBTI analysis
- **Format Result**: Formats the AI response for display
- **Respond to Webhook**: Returns the MBTI result to the HTML page
- **Send Email**: Sends an email with results and answers to the user

## Customization

### Changing Questions

To modify questions in `index.html`:
1. Find the question containers (`.question-container`)
2. Update the question text in `.question-text`
3. Update the image URLs in the `<img>` tags
4. Update the `data-answer` attributes to match MBTI preferences (introvert/extravert, sensing/intuition, thinking/feeling, judging/perceiving)

### Customizing AI Prompt

In the n8n workflow, edit the "AI Analysis - OpenAI" node to modify the system and user prompts for different analysis styles.

### Email Template

Edit the "Send Email" node's message field to customize the email template HTML.

## Troubleshooting

**Webhook not receiving data:**
- Make sure the workflow is activated
- Check that the webhook URL in HTML matches the n8n webhook URL
- Verify CORS settings if hosting on a different domain

**OpenAI API errors:**
- Verify your API key is correct
- Check that you have sufficient API credits
- Ensure the model name is correct (gpt-4, gpt-3.5-turbo, etc.)

**Email not sending:**
- Verify SMTP credentials are correct
- Check that your email provider allows SMTP access
- For Gmail, you may need to use an "App Password" instead of your regular password

## License

This project is provided as-is for educational and personal use.
