# 🚗 Electra Cars: AI-Driven Warranty Claim Solution

## 📋 Overview
Electra Cars manages an installed base of 300,000+ vehicles. This project transforms their highly manual, email-based warranty claim process into a streamlined, AI-driven experience. By leveraging Salesforce Agentforce, Automotive Cloud, and Slack, this solution automates dealer intake, digitizes records, and provides internal approvers with an intelligent, conversational workspace to make faster, data-backed decisions.

## ✨ Key Features
* **Conversational Intake:** Eliminates static web forms. Dealers use WhatsApp to chat with an AI Agent that validates VINs and collects necessary diagnostic codes and photos.
* **Automated Digitization:** Seamlessly converts unstructured chat data into structured `Claim`, `Vehicle`, and `Claim Item` records in Automotive Cloud.
* **Intelligent Routing & Context:** Evaluates claim amounts to dynamically route to the correct approver (Standard vs. Senior) and enriches the claim with real-time Data Cloud insights (Telematics & Dealer Trust Scores).
* **AI-Assisted Slack Approvals:** Internal claim approvers receive rich-text Slack notifications. They can @-mention an internal AI Copilot to ask the dealer follow-up questions directly from the Slack thread.
* **1-Click Resolution:** Approvers execute decisions via interactive Slack buttons, automatically updating the database and generating unique Authorization Codes for the dealer.

## 🏗️ Solution Architecture

### Technologies Used
* **Salesforce Automotive Cloud:** Core system of record (`Vehicle`, `AssetWarranty`, `Claim`).
* **Salesforce Agentforce:** Powers both the external Dealer-Facing Agent and the internal Employee Assistant.
* **Salesforce Data Cloud:** Harmonizes IoT telematics (`Active_DTC_Codes`) and historical dealer performance metrics.
* **Salesforce Digital Engagement:** Manages the WhatsApp/SMS omnichannel routing.
* **Slack (Salesforce for Slack):** Serves as the operational command center for claim approvers.

### The Agents
1. **Dealer-Facing Agent (External):** Intercepts inbound WhatsApp messages, validates warranty status, and initiates the claim.
2. **Employee Agent (Internal):** Operates in Slack. Translates approver commands into outbound WhatsApp messages using native `sendConversationMessages` actions.

---

## ⚙️ Prerequisites & Setup

### Licensing Requirements
* Automotive Cloud License
* Agentforce / Copilot Permissions
* Digital Engagement Add-on (for WhatsApp)
* Data Cloud Provisioning
* Slack Enterprise or Pro workspace

### Implementation Steps

#### 1. Data Model Configuration
1. Ensure `Vehicle`, `AssetWarranty`, and `Claim` objects are active.
2. Configure Data Cloud Data Model Objects (DMOs) for **Vehicle Telematics** and **Partner Performance** and map them to the core `Vehicle` and `Account` objects.

#### 2. Flow Automation
Deploy the following Flows:
* `AgentAction_SendToWhatsApp` (Autolaunched): Uses the standard *Send Conversation Messages* action.
* `Claim_AfterInsert_Routing` (Record-Triggered): Evaluates `Estimated_Amount__c` for routing and sends the initial notification to Slack.
* `Slack_Claim_Decision` (Screen Flow): The interactive Slack UI for Approving/Rejecting claims.

#### 3. Agentforce Configuration
1. **Dealer Agent:** Configure via Agentforce Builder. Connect to the WhatsApp channel. Add custom Actions for VIN validation and Claim creation.
2. **Employee Agent:** Configure via Agentforce Builder using the *Internal Copilot* template. Expose to Slack. Provide system instructions to execute the `AgentAction_SendToWhatsApp` flow when an approver asks to contact a dealer.

#### 4. Slack Integration
1. Install the *Salesforce for Slack* application.
2. Create the `Warranty Claim Alert` Slack Message Template in Salesforce Setup.
3. Embed the `Slack_Claim_Decision` screen flow button into the template payload.

---

## 🧪 Testing the End-to-End Flow
1. **Initiate:** As a test dealer, send a WhatsApp message requesting a part replacement for a valid VIN.
2. **Validate Intake:** Confirm the bot asks for the part number, diagnostic code, and a photo.
3. **Check Database:** Verify the `Claim` record is created in Automotive Cloud.
4. **Review Alert:** Open Slack and verify the rich-text alert arrives in the designated channel with Data Cloud insights.
5. **Test Copilot:** @-mention the Agentforce bot in the Slack thread and say, *"Ask the dealer for a clearer photo."* Verify the message arrives on your test WhatsApp device.
6. **Approve:** Click the *Review Claim* button in Slack, select *Approve*, and verify the final confirmation message (with Auth Code) is sent back via WhatsApp.

---
*Built for Electra Cars Warranty Operations Modernization*
