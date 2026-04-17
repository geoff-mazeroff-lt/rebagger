# Problem Statement Template

When refining a problem statement, check for coverage of the following areas.
Not all areas apply to every project — use judgment about which are relevant
and which can be deferred. The goal is to surface gaps early, not to force
exhaustive documentation.

---

## Problem & Purpose

- [ ] **What problem does this solve?** Who experiences this problem today?
- [ ] **Who are the users?** Are there different user roles or personas?
- [ ] **What is the core workflow?** What does a user do from start to finish?
- [ ] **What does success look like?** How will you know this tool is working?

## Scope & Constraints

- [ ] **What is explicitly out of scope?** What will this NOT do (at least initially)?
- [ ] **Are there hard constraints?** (e.g., must work offline, must run on
      specific hardware, must integrate with an existing system)
- [ ] **Is there a timeline or deadline?** Does this affect scope decisions?
- [ ] **Is this for personal use, a team, or public/commercial use?**
      This affects decisions about auth, scalability, and licensing.

## Data & Integrations

- [ ] **What data does the app work with?** Where does it come from?
- [ ] **Are there external APIs or services it must integrate with?**
- [ ] **Are there data privacy or compliance requirements?**
      (e.g., HIPAA, GDPR, PCI)
- [ ] **Does it need to import/export data?** In what formats?

## User Experience

- [ ] **Is this a web app, CLI tool, mobile app, desktop app, or API?**
- [ ] **Does it need to work on specific devices or browsers?**
- [ ] **Are there accessibility requirements?**
- [ ] **Is there an existing design language or brand to follow?**

## Operational Context

- [ ] **Who will maintain this after it's built?** Solo developer? Team?
- [ ] **Is there an existing codebase or infrastructure this connects to?**
- [ ] **Are there preferences about hosting or deployment?**
      (e.g., "we use AWS," "must be self-hosted")
- [ ] **What is the expected scale?** Number of users, volume of data?
