# PlanLock: A Privacy-Request Agent Whose Permissions Come From an Approved Plan

Built for the "Agents, your rules: Bedrock Managed Agents build" hackathon.

## The job
Handle a customer's data access request: export one verified customer's profile and support transcripts, and nothing else.

## The idea
The agent writes a plan. A human approves it. A deterministic Lambda (no LLM) compiles the approved plan into an STS session policy, and the tools run under those credentials only. Anything outside the plan is denied by IAM, not by the model.

## Status
- [ ] Rules written (RULES.md)
- [ ] Fake data created
- [ ] Plan-to-policy Lambda working
- [ ] Agent built on Amazon Bedrock Managed Agents / AgentCore managed harness
- [ ] Guardrail attached
- [ ] Stress test (15+ attacks, including prompt injection)
- [ ] No-LLM replay
- [ ] Failure documented (failure-log.md)
- [ ] Architecture diagram
- [ ] Screenshots

## Repo contents (planned)
- `RULES.md`: plain-language rule set
- `failure-log.md`: what broke and how it was fixed
- `lambda/`: plan validation and session-policy compiler
- `attacks.json`, `run_attacks.py`, `replay_no_llm.py`: stress test
- `docs/`: architecture diagram and screenshots

## Data
All data is fictional. No real personal information is used.

## License
MIT. See LICENSE.
