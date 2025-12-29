<p align="center">
  <img src="https://harbyx.com/logos/harbyx_logo.jpeg" alt="AASP Logo" width="120" />
</p>

<h1 align="center">AASP Policy Library</h1>

<p align="center">
  <strong>Production-ready security policies for AI agents</strong><br>
  Copy, customize, deploy. Secure your agents in minutes.
</p>

<p align="center">
  <a href="https://github.com/aasp-platform/policy-library/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue?style=for-the-badge" alt="License"></a>
  <a href="https://github.com/aasp-platform/policy-library/stargazers"><img src="https://img.shields.io/github/stars/aasp-platform/policy-library?style=for-the-badge" alt="Stars"></a>
  <a href="https://github.com/aasp-platform/policy-library/network/members"><img src="https://img.shields.io/github/forks/aasp-platform/policy-library?style=for-the-badge" alt="Forks"></a>
  <a href="https://discord.gg/aasp"><img src="https://img.shields.io/badge/discord-join-7289da?style=for-the-badge&logo=discord&logoColor=white" alt="Discord"></a>
</p>

---

## What's Inside

Ready-to-use security policies for common AI agent use cases. Each policy is battle-tested and customizable.

```
policies/
├── security/           # Prevent attacks & data breaches
├── compliance/         # SOC2, GDPR, PCI-DSS, HIPAA
├── operations/         # Business logic & rate limiting
└── industry/           # Finance, healthcare, legal
```

---

## Quick Start

### 1. Browse & Copy

Find a policy that matches your use case:

```bash
# Clone the library
git clone https://github.com/aasp-platform/policy-library.git

# Browse policies
ls policies/security/
```

### 2. Import to AASP

**Via Dashboard:**
1. Go to [AASP Dashboard](https://app.harbyx.com/dashboard/policies)
2. Click "Create Policy"
3. Paste the policy JSON

**Via API:**
```bash
curl -X POST https://app.harbyx.com/api/v1/policies \
  -H "Authorization: Bearer aasp_live_xxx" \
  -H "Content-Type: application/json" \
  -d @policies/security/sql-injection-prevention.json
```

### 3. Customize

Modify patterns and thresholds for your environment:

```json
{
  "name": "High-Value Transaction Approval",
  "rules": [{
    "conditions": [{
      "field": "params.amount",
      "operator": "gt",
      "value": 10000  // ← Adjust threshold
    }],
    "effect": "REQUIRE_APPROVAL"
  }]
}
```

---

## Policy Categories

### Security

Protect against attacks and unauthorized access.

| Policy | Description | Effect |
|--------|-------------|--------|
| [`sql-injection-prevention`](policies/security/sql-injection-prevention.json) | Block DROP, DELETE, TRUNCATE | BLOCK |
| [`pii-protection`](policies/security/pii-protection.json) | Detect SSN, credit cards in queries | BLOCK |
| [`api-rate-limiting`](policies/security/api-rate-limiting.json) | Prevent API abuse | BLOCK |
| [`secrets-detection`](policies/security/secrets-detection.json) | Block API keys, passwords in output | BLOCK |

### Compliance

Meet regulatory requirements.

| Policy | Description | Effect |
|--------|-------------|--------|
| [`high-value-transactions`](policies/compliance/high-value-transactions.json) | Approval for transactions > $10k | REQUIRE_APPROVAL |
| [`data-export-control`](policies/compliance/data-export-control.json) | Limit bulk data exports | REQUIRE_APPROVAL |
| [`external-communication`](policies/compliance/external-communication.json) | Review external emails | REQUIRE_APPROVAL |
| [`audit-logging`](policies/compliance/audit-logging.json) | Log all sensitive operations | ALLOW (logged) |

### Operations

Enforce business logic and operational constraints.

| Policy | Description | Effect |
|--------|-------------|--------|
| [`business-hours-only`](policies/operations/business-hours-only.json) | Restrict to working hours | BLOCK |
| [`file-system-protection`](policies/operations/file-system-protection.json) | Protect sensitive directories | BLOCK |
| [`cloud-resource-limits`](policies/operations/cloud-resource-limits.json) | Prevent runaway cloud costs | REQUIRE_APPROVAL |

### Industry-Specific

Tailored for regulated industries.

| Policy | Industry | Description |
|--------|----------|-------------|
| [`hipaa-phi-protection`](policies/industry/hipaa-phi-protection.json) | Healthcare | Protect patient data |
| [`pci-cardholder-data`](policies/industry/pci-cardholder-data.json) | Finance | PCI-DSS compliance |
| [`sox-financial-controls`](policies/industry/sox-financial-controls.json) | Finance | SOX audit requirements |
| [`legal-privilege`](policies/industry/legal-privilege.json) | Legal | Protect privileged communications |

---

## Policy Format

All policies follow this structure:

```json
{
  "name": "Policy Name",
  "description": "What this policy does and why",
  "priority": 80,
  "enabled": true,
  "rules": [
    {
      "id": "unique-rule-id",
      "actionType": "tool_call",
      "targetPattern": "^send_email$",
      "conditions": [
        {
          "field": "params.to",
          "operator": "matches",
          "value": "@external\\.com$"
        }
      ],
      "effect": "REQUIRE_APPROVAL"
    }
  ]
}
```

### Fields Reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Human-readable name |
| `description` | string | Yes | Purpose and use case |
| `priority` | number | Yes | 1-100, higher = first |
| `enabled` | boolean | Yes | Active state |
| `rules` | array | Yes | List of rules |

### Rule Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | Unique identifier |
| `actionType` | string | Yes | `tool_call`, `api_call`, `db_query`, `file_access` |
| `targetPattern` | string | Yes | Regex for target matching |
| `conditions` | array | No | Additional match conditions |
| `effect` | string | Yes | `ALLOW`, `BLOCK`, `REQUIRE_APPROVAL` |

### Condition Operators

| Operator | Type | Example |
|----------|------|---------|
| `eq` | Exact match | `{"field": "env", "operator": "eq", "value": "prod"}` |
| `neq` | Not equal | `{"field": "role", "operator": "neq", "value": "admin"}` |
| `gt` / `gte` | Greater than | `{"field": "amount", "operator": "gt", "value": 1000}` |
| `lt` / `lte` | Less than | `{"field": "count", "operator": "lt", "value": 100}` |
| `contains` | Substring | `{"field": "query", "operator": "contains", "value": "DROP"}` |
| `matches` | Regex | `{"field": "email", "operator": "matches", "value": "@company\\.com$"}` |

---

## Creating Custom Policies

### Step 1: Identify the Risk

What action do you want to control?

```
Agent might: Send emails to external addresses
Risk: Data exfiltration, phishing
Control: Require approval for external recipients
```

### Step 2: Define the Rule

```json
{
  "id": "external-email-approval",
  "actionType": "tool_call",
  "targetPattern": "^send_email$",
  "conditions": [
    {
      "field": "params.to",
      "operator": "matches",
      "value": "^(?!.*@yourcompany\\.com).*$"
    }
  ],
  "effect": "REQUIRE_APPROVAL"
}
```

### Step 3: Test & Deploy

```bash
# Test with aasp_test_ key first
export AASP_API_KEY="aasp_test_xxx"

# Run your agent, verify policy triggers
python test_agent.py

# Deploy to production
export AASP_API_KEY="aasp_live_xxx"
```

---

## Contributing

We welcome new policies! Here's how:

### Adding a Policy

1. Fork this repository
2. Create your policy in the appropriate category
3. Include comprehensive description and examples
4. Test with AASP
5. Submit a pull request

### Policy Guidelines

- **Clear naming**: `action-what-it-does.json`
- **Good description**: Explain the risk and control
- **Reasonable defaults**: Should work out-of-the-box
- **Customization notes**: Document what to adjust

### Good First Issues

Look for [`good-first-issue`](https://github.com/aasp-platform/policy-library/labels/good-first-issue) labels.

---

## Related Projects

| Project | Description |
|---------|-------------|
| [aasp-server](https://github.com/aasp-platform/aasp-server) | AASP control plane |
| [aasp-sdk](https://github.com/aasp-platform/aasp-sdk) | Python SDK |
| [aasp-docs](https://github.com/aasp-platform/aasp-docs) | Documentation |

---

## License

MIT License - see [LICENSE](LICENSE) for details.

Policies are provided as-is. Review and customize for your specific requirements.

---

<p align="center">
  <strong>Built by <a href="https://harbyx.com">Harbyx</a></strong><br>
  <sub>Community-powered security for AI agents.</sub>
</p>
