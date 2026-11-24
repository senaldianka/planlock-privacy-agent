# Failure log

Write an entry every time something breaks. This becomes the "rule that broke" section of the project write-up.

## Template

### Entry N: short title
- **Date/time:**
- **Rule affected:** (e.g., Rule 2: only the verified customer's records)
- **What I did / what attacked it:**
- **What happened (expected vs actual):**
- **Which layer caught it, or failed to:** (Guardrail / agent / Lambda validation / IAM)
- **Fix:**
- **Before numbers:** (e.g., 3 of 15 attacks succeeded)
- **After numbers:**
- **Screenshot/file:**

## Entries

### Entry 1: Claude Sonnet 5.5 blocked in my account
- Date: 2026-11-23
- What happened: Playground returned AccessDeniedException saying
  anthropic.claude-sonnet-5-5 is not available for this account.
- Layer: account-level model access (not related to the agent's rules)
- Fix: switched to Claude Sonnet 4.6, which responded normally in ap-southeast-2.

### Entry 2: Agent name with a hyphen rejected
- Date: 2026-11-23
- What happened: Creating the harness agent as "planlock-agent" failed with an error saying the
  name must start with a letter and contain only letters, numbers, and underscores.
- Layer: AgentCore console input validation (not related to the agent's rules)
- Fix: renamed the agent to planlock_agent, and used underscores for other resource names.

### Entry 3: Harness defaults were too permissive for a privacy agent
- Date: 2026-11-23
- What happened: The harness form defaulted to Allowed tools = "*" (including built-in file
  system and shell operations) and to Memory enabled (long-term storage of conversation history).
- Layer: harness configuration (tool surface and data retention)
- Fix: turned Memory off, and lowered Max Iterations to 10, Timeout to 5 minutes, and Max tokens to 1024.

### Entry 4: Lambda was attached to a broad PowerUserAccess role
- Date: 2026-11-23
- What happened: The Lambda's execution role was a pre-existing role with PowerUserAccess,
  which could read the customers table and bucket directly, bypassing the per-customer session.
- Layer: IAM (execution role)
- Fix: created a dedicated role with only basic logging permissions; the Lambda can reach
  customer data only through the scoped STS session.

### Entry 5: Guardrail denied topic blocked legitimate exports
- Date/time: 2026-11-24
- Rule affected: Rule 2 (only the verified customer's records), plus usability of the legitimate export
- What I did / what attacked it: Tested the guardrail in the Bedrock console with two normal requests: "Please export my data. My customer ID is cust_001." and "Export my profile and my support transcripts."
- What happened (expected vs actual): Expected both to pass. Both were blocked. The trace showed the "Other customers data" denied topic with Detected: TRUE, while   the prompt attack and Legal advice filters did nothing. The topic matches by meaning, so ordinary export wording looked close to its sample phrases.
- Which layer caught it, or failed to: Guardrail (false positive)
- Fix: Removed the "Other customers data" topic. The rule is enforced by the session policy in IAM and the Lambda's single-customer plan validation, so no protection is lost. Narrowing the definition and samples first did not stop the false positive.

