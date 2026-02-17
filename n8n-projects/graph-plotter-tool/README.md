# n8n AI Graph Plotter Tool

An automated workflow for **n8n** that enables AI Agents to generate and display real-time instrument data as visual graphs directly within a chat interface.

<img width="1641" height="798" alt="image" src="https://github.com/user-attachments/assets/31ad9342-3efe-4522-b007-9fc900f0b799" />


## 🚀 Project Overview

This project provides a robust plotting tool for n8n to be used by an AI agent. It allows the agent to return real-time instrument data from an IOT Hub as a properly formatted graph directly in the user's chat window.

The AI agent identifies the specific `instrument_id` from the user's prompt and passes it as a parameter to trigger this workflow, ensuring the correct data is pulled for the right device.

### Key Features
* **Real-time Data Fetching:** Communicates with the IOT Hub using the **Direct Communication Protocol**.
* **Dynamic Routing:** Uses a Switch node to handle different plot types (Bar, Line, etc.) by pulling from specific IOT Hub endpoints.
* **Quickchart.io Integration:** A dedicated Code node transforms raw IOT data into a URL using the Quickchart.io protocol.
* **Seamless AI Feedback:** The workflow returns a final URL which the AI agent renders as an image in the chat.

---

## 🛠️ Workflow Logic

The workflow follows a structured pipeline to ensure data integrity and visual accuracy:

1.  **Trigger:** Tool call received from the AI Agent containing the `instrument_id` and `type`.
2.  **Switching:** The workflow routes the request based on the required graph type (e.g., Handling Bar Graph vs. Handling Line Graph).
3.  **Authentication & API Call:** Connects to the IOT Hub (Auth currently handled within the node).
4.  **Formatting:** Raw data is processed and formatted for visualization.
5.  **URL Construction:** The final chart URL is generated.
6.  **Merging:** Results from different branches are merged into a single output to be returned as the tool call result.

---

## ⚠️ Error Handling

To ensure a smooth user experience, a dedicated error handling logic is integrated into the workflow:
* **Error Flags:** The workflow monitors for IOT Hub endpoint failures (e.g., if an endpoint doesn't reply).
* **Dedicated Handling:** Specific nodes are present to catch errors and prevent the workflow from crashing, allowing the AI to inform the user of the connection status gracefully.

---

## 📈 Future Upgrades

We are looking to improve the scalability and reliability of this tool through the following updates:

* **Dynamic Authentication:** Move hard-coded credentials to a **Supabase** database. n8n will pull fresh credentials before each communication to ensure the workflow doesn't break if keys are rotated.
* **Expanded Visualization:** Adding support for more plot types to handle diverse user requests.
* **Independent Error Logging:** Implementing a database to log timestamps of IOT Hub endpoint failures for better diagnostic tracking.

---

## 🔧 Setup & Usage

1.  **Import Workflow:** Import the `.json` workflow file into your n8n instance.
2.  **Configure API:** Update the IOT Hub API Tool nodes with your specific endpoint details.
3.  **AI Integration:** Map this workflow as a tool in your AI Agent (in N8N) using the expected input parameters.

---
## Screenshot of the workflow
<img width="998" height="756" alt="image" src="https://github.com/user-attachments/assets/1ab11da2-8fd5-4b07-854e-b0442be9950c" />

