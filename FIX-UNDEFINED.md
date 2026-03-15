# Fix for [undefined] Result

## The Problem
You're getting `[undefined]` with expression:
```
{{ JSON.parse($json.body).choices[0].message.content }}
```

## Why It's Undefined

In the "Format Result" node:
- `$json` refers to the **input** from the previous node
- But the structure might be different than expected
- Need to **explicitly reference** the "AI Analysis - OpenAI" node

## The Correct Expression

In "Format Result" node, set `mbtiResult` to:

```
{{ JSON.parse($('AI Analysis - OpenAI').item.json.body).choices[0].message.content }}
```

**Key difference:** Use `$('AI Analysis - OpenAI')` instead of `$json`

## Step-by-Step Fix

1. **Go to n8n → Your Workflow**
2. **Click "Format Result" node**
3. **Find the `mbtiResult` field**
4. **Change the value to:**
   ```
   {{ JSON.parse($('AI Analysis - OpenAI').item.json.body).choices[0].message.content }}
   ```
5. **Save the workflow**
6. **Test again**

## Alternative Expressions (If Above Doesn't Work)

### Option 1: If body is already parsed
```
{{ $('AI Analysis - OpenAI').item.json.body.choices[0].message.content }}
```

### Option 2: Check multiple locations
```
{{ $('AI Analysis - OpenAI').item.json.body?.choices?.[0]?.message?.content || $('AI Analysis - OpenAI').item.json.choices?.[0]?.message?.content }}
```

### Option 3: With error handling
```
{{ (() => {
  try {
    const aiNode = $('AI Analysis - OpenAI');
    const body = aiNode.item.json.body;
    if (typeof body === 'string') {
      return JSON.parse(body).choices[0].message.content;
    } else {
      return body.choices[0].message.content;
    }
  } catch(e) {
    return 'Error: ' + e.message;
  }
})() }}
```

## Debugging

If still undefined:

1. **Check "AI Analysis - OpenAI" node output:**
   - Go to Executions → Latest execution
   - Click "AI Analysis - OpenAI" node
   - What does it show?
   - Is `body` a string or object?

2. **Test with simpler expression:**
   ```
   {{ $('AI Analysis - OpenAI').item.json.body }}
   ```
   This will show you the raw body content

3. **Then adjust based on what you see**

## Most Common Issue

The expression `{{ JSON.parse($json.body)... }}` doesn't work because:
- `$json` in "Format Result" might not have `body` field
- Or `body` might be in a different location
- **Solution:** Explicitly reference the AI node: `$('AI Analysis - OpenAI')`

## Verification

After applying the fix:
1. Check "Format Result" node output in execution
2. `mbtiResult` should show: "MBTI Type: ENFP\n\nAssessment Summary:..."
3. Browser console should show the result
4. Result should display on HTML page
