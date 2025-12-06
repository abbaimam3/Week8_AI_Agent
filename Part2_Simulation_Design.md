# Part 2: Simulation Design - Predictive Maintenance Agent (n8n)

## Workflow Overview
This simulation demonstrates the "Sentinel" agent (Predictive Maintenance) using **n8n**. The workflow ingests sensor data, analyzes it using an AI Agent (LLM), and triggers maintenance actions if a failure is predicted.

### 1. Trigger: Webhook (Sensor Data)
*   **Node Name:** `Sensor Data Ingest`
*   **Type:** `Webhook`
*   **Method:** `POST`
*   **Payload Example:**
    ```json
    {
      "machine_id": "CNC-04",
      "vibration_level": 8.5,
      "temperature": 102,
      "sound_decibels": 95
    }
    ```

### 2. AI Agent: Analysis (OpenAI)
*   **Node Name:** `AI Analysis Agent`
*   **Type:** `OpenAI Chat Model`
*   **System Prompt:**
    "You are a Predictive Maintenance Expert. Analyze the incoming sensor data.
    - Normal Vibration: < 5.0
    - Normal Temp: < 90
    - If values exceed thresholds, calculate a 'Failure Probability' (0-100%).
    - Return a JSON object: { 'risk_score': 85, 'reason': 'High vibration detected', 'action_needed': true }"

### 3. Logic: Conditional Switch
*   **Node Name:** `Check Risk Level`
*   **Type:** `If`
*   **Condition:** `risk_score > 80`

### 4. Action: Alerting
*   **True Path (High Risk):**
    *   **Node Name:** `Slack Alert`
    *   **Action:** Send message to `#maintenance-team`: "URGENT: Machine {{machine_id}} is at {{risk_score}}% risk of failure. Reason: {{reason}}."
    *   **Node Name:** `Create Jira Ticket`
    *   **Action:** Create ticket "Inspect CNC-04 immediately."
*   **False Path (Low Risk):**
    *   **Node Name:** `Log Data`
    *   **Action:** Append data to Google Sheet for historical training.

## How to Use
1.  Import the provided `n8n_workflow_simulation.json` file into your n8n instance.
2.  Configure your OpenAI API Key and Slack/Jira credentials.
3.  Test by sending a POST request to the Webhook URL with the sample payload.
