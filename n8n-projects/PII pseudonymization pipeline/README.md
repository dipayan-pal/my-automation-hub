# Secure PII Redaction & Re-identification Workflow

An n8n-powered workflow that protects Personally Identifiable Information (PII) before sending documents to cloud-based LLMs. It uses local LLM-based PII detection, reversible encryption, pseudonymization, and controlled re-identification to minimize the exposure of sensitive information.

## Overview

The workflow follows a **Detect → Redact → Process → Re-identify** approach.

1. Extracts text from uploaded documents.
2. Detects PII using a local LLM.
3. Replaces sensitive values with unique placeholders.
4. Encrypts original PII using AES-256-GCM.
5. Stores encrypted PII mappings in PostgreSQL.
6. Sends only redacted text to a cloud LLM for processing.
7. Retrieves and decrypts the original PII mappings.
8. Replaces placeholders in the cloud LLM response with the original values.

## Key Features

- **Local PII Detection:** Uses a locally hosted LLM to identify sensitive information without sending the original document to an external LLM.
- **Dynamic Pseudonymization:** Generates unique placeholders such as `[PERSON_001]`, `[EMAIL_001]`, and `[PHONE_001]`.
- **Authenticated Encryption:** Encrypts original PII using AES-256-GCM through the n8n Crypto node.
- **Secure Mapping Storage:** Stores encrypted PII, placeholder mappings, PII categories, and session identifiers in PostgreSQL.
- **Cloud LLM Integration:** Sends pseudonymized text to a cloud LLM while keeping the original PII out of the cloud request.
- **Re-identification:** Decrypts stored mappings and reconstructs the cloud LLM response by replacing placeholders with original values.
- **Session Isolation:** Uses execution-specific session IDs to associate PII mappings with the correct workflow execution.
- **Execution Data Protection:** Configures workflow-level execution settings to avoid retaining sensitive execution inputs and outputs unnecessarily.

## Architecture

```text
         Document Upload
                |
                v
         Text Extraction
                |
                v
       Local LLM - PII Detection
                |
                v
        Placeholder Generation
                |
          +-----+------+
          |            |
          v            v
     AES-256-GCM    Original PII
      Encryption    for Redaction
          |
          v
      PostgreSQL
   Encrypted Mappings
          |
          |
   Redacted Document
          |
          v
       Cloud LLM
          |
          v
    LLM Response with
      Placeholders
          |
          v
   PostgreSQL Select
          |
          v
     AES-256-GCM
       Decryption
          |
          v
    Re-identification
          |
          v
     Final Response
```

## Technology Stack

| Component | Technology |
|---|---|
| Workflow Automation | n8n |
| PII Detection | Locally hosted LLM |
| Cloud Processing | Cloud LLM API |
| Encryption | AES-256-GCM |
| Encryption Management | n8n Crypto Credentials |
| Database | PostgreSQL |
| Document Processing | n8n Extract from File node |

## Security Considerations

- Original PII is encrypted before being persisted in PostgreSQL.
- Only pseudonymized content is sent to the cloud LLM.
- Encryption and decryption use the same protected passphrase credential.
- PII mappings are associated with individual execution sessions.
- Workflow execution data saving is restricted to reduce unnecessary retention of sensitive information.

**Important:** This implementation provides reversible pseudonymization, not complete anonymization. PII is still present in the workflow's processing environment and must be protected through appropriate access controls, execution-data policies, database security, and encryption-key management.

## Future Improvements

- Integrate AWS Secrets Manager for centralized encryption-key management.
- Implement key rotation and key versioning.
- Improve PII detection accuracy and validation.
- Introduce robust error handling, retries, and recovery mechanisms.
- Strengthen access control and audit logging.
- Add support for additional document formats and PII categories.

---

*Built with n8n to demonstrate privacy-conscious integration of sensitive business data with cloud-based AI systems.*
