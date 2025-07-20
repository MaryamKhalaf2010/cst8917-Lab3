# cst8917-Lab3
# Microsoft Teams Chat Content Moderation with Logic App
## by Maryam Khalaf

## 1. Logic App Flowchart 📸
<img width="1900" height="1888" alt="teams-moderation-workflow-final" src="https://github.com/user-attachments/assets/a1dbb928-4b23-470a-9e88-b7ab7aff4b08" />

<img width="782" height="994" alt="Logic App" src="https://github.com/user-attachments/assets/63da3ce3-35ed-426a-a98a-f38586d79e93" />


## 2. Logic App Setup Description

This Logic App monitors Microsoft Teams for new messages, analyzes the message sentiment using Azure Cognitive Services, and responds with alerts if negative sentiment is detected.

### Step-by-Step Breakdown:

1. **Trigger** – `When a new channel message is added`

   - This built-in Microsoft Teams trigger activates the Logic App whenever a new message is posted in a specific Teams channel.
   - It provides the message content as dynamic input to the next steps.

2. **Action** – `HTTP`

   - An HTTP POST request is sent to Azure Cognitive Services (Text Analytics Sentiment API).
   - The body of the request contains the Teams message in JSON format with fields like `language`, `id`, and `text`.

3. **Action** – `Parse JSON`

   - This action takes the raw JSON response from the sentiment API and parses it to extract the sentiment value.
   - The key property extracted is `body('Parse_JSON')?['documents'][0]['sentiment']`.

4. **Condition** – Check Sentiment Result

   - A condition checks whether the sentiment returned from the API is **equal to "negative"**.
   - Expression example:
     ```
     equals(body('Parse_JSON')?['documents'][0]['sentiment'], 'negative')
     ```

5. **If True (Negative Sentiment Detected)**:

   - **Send Email** (Outlook – `Send an email V2`):  
     Sends an email alert to the administrator including:

     - A warning about the negative message.
     - The sentiment score and the original message text.

   - **Post Message in Teams**:  
     Uses the Microsoft Teams connector `Post message in a chat or channel` to send a formatted alert back into the channel.
     - The message uses HTML-style formatting to include bold/italic and icons.
     - Example message:
       ```
       ⚠️ <b>Alert:</b> A message was flagged with <i>negative</i> sentiment.
       <b>Original message:</b> "I'm not happy with this project."
       ```

6. **If False (Non-Negative Sentiment)**:
   - No action is taken. The Logic App exits quietly.

## 3. Azure Cognitive Services Details

- **Service Used**: Text Analytics – Sentiment Analysis API
- **Authentication**: API Key + Endpoint
- **Endpoint Format**: `https://<region>.api.cognitive.microsoft.com/text/analytics/v3.1/sentiment`
- **Method**: POST with message body containing `text`, `language`, and `id`

## 4. Testing the Workflow

- A test message with negative wording was sent through Microsoft Teams.
- The Logic App successfully detected negative sentiment.
- Both an email and a Teams alert were triggered.
- Screenshots of the email and Teams messages are included.

### Screenshots

- Logic App run history
<img width="2246" height="1176" alt="run_history" src="https://github.com/user-attachments/assets/488503dc-8437-4b75-8036-cb8fb29b7584" />

- Teams alert message
<img width="2108" height="1318" alt="Teams alert message" src="https://github.com/user-attachments/assets/05804f97-a650-4a41-aeee-912438aeffd2" />

- Email<img width="2208" height="1180" alt="Email notification" src="https://github.com/user-attachments/assets/f70bc935-960f-4871-8fdb-f5ddc7c85f9f" />
 notification

## 5. Challenges and Resolutions

- **Issue**: API key format error  
  **Fix**: Regenerated key and updated Logic App HTTP headers.
- **Issue**: JSON parsing failed initially  
  **Fix**: Used sample output from API to generate the schema automatically in `Parse JSON`.

## 6. Recommendations

- Future improvements could include:
  - Using Azure Content Moderator for profanity or policy violations.
  - Adding severity-based actions based on confidence scores.
  - Logging flagged messages in a database for review.

## 7. Demo Video 🎥

Watch the full demo here: _(Insert YouTube link)_
