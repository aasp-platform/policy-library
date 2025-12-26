# AASP Policy Library

Community-curated security policy templates for the AI Agent Security Platform.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## What is This?

This repository contains ready-to-use security policies for AASP. These policies help you:

- **Prevent data breaches** - Block unauthorized data access
- **Enforce compliance** - Meet SOC 2, GDPR, PCI-DSS requirements
- **Control AI agents** - Require approval for sensitive operations
- **Audit everything** - Log all agent actions for review

## Quick Start

### 1. Copy a Policy

Browse the `policies/` directory and copy the JSON policy you need.

### 2. Import to AASP

```bash
# Using the AASP Dashboard
1. Go to https://aasp-mvp.aminereg.com/dashboard/policies
2. Click "Create Policy"
3. Paste the policy JSON
```

### 3. Customize

Modify the policy to match your requirements:
- Adjust `targetPattern` regex patterns
- Change `conditions` thresholds
- Set appropriate `priority` levels

## Policy Categories

### Security (`policies/security/`)

| Policy | Description |
|--------|-------------|
| `sql-injection-prevention.json` | Block dangerous SQL operations |
| `pii-protection.json` | Protect personally identifiable information |
| `api-rate-limiting.json` | Prevent API abuse and DoS |

### Compliance (`policies/compliance/`)

| Policy | Description |
|--------|-------------|
| `high-value-transactions.json` | Require approval for large transactions |
| `data-export-control.json` | Control bulk data exports |
| `external-communication.json` | Monitor external emails/messages |

### Operations (`policies/operations/`)

| Policy | Description |
|--------|-------------|
| `business-hours-only.json` | Restrict actions to business hours |
| `file-system-protection.json` | Control file system access |
| `cloud-resource-management.json` | Protect cloud infrastructure |

## Policy Format

All policies use this JSON structure:

```json
{
  "name": "Policy Name",
  "description": "What this policy does",
  "priority": 80,
  "enabled": true,
  "rules": [
    {
      "id": "rule-unique-id",
      "actionType": "tool_call",
      "targetPattern": "regex_pattern",
      "conditions": [
        {
          "field": "params.fieldName",
          "operator": "eq",
          "value": "some_value"
        }
      ],
      "effect": "BLOCK"
    }
  ]
}
```

### Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Human-readable policy name |
| `description` | string | What the policy does |
| `priority` | number | 1-100, higher = evaluated first |
| `enabled` | boolean | Whether policy is active |
| `rules` | array | List of rules to evaluate |

### Rule Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique identifier for the rule |
| `actionType` | string | `tool_call`, `api_call`, `db_query`, `file_access` |
| `targetPattern` | string | Regex to match target (tool name, endpoint, etc.) |
| `conditions` | array | Additional conditions to match |
| `effect` | string | `ALLOW`, `BLOCK`, or `REQUIRE_APPROVAL` |

### Condition Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `eq` | Equals | `{"field": "status", "operator": "eq", "value": "active"}` |
| `neq` | Not equals | `{"field": "role", "operator": "neq", "value": "admin"}` |
| `gt` | Greater than | `{"field": "amount", "operator": "gt", "value": 1000}` |
| `lt` | Less than | `{"field": "count", "operator": "lt", "value": 100}` |
| `gte` | Greater or equal | `{"field": "priority", "operator": "gte", "value": 5}` |
| `lte` | Less or equal | `{"field": "risk", "operator": "lte", "value": 3}` |
| `contains` | String contains | `{"field": "query", "operator": "contains", "value": "password"}` |
| `matches` | Regex match | `{"field": "email", "operator": "matches", "value": "@company\\.com$"}` |

## Contributing

We welcome contributions! To add a new policy:

1. Fork this repository
2. Add your policy JSON to the appropriate category
3. Include a description in the policy file
4. Submit a pull request

### Guidelines

- Use descriptive policy and rule names
- Include comments explaining the policy purpose
- Test your policy before submitting
- Follow the existing naming conventions

## License

MIT License - see [LICENSE](LICENSE) for details.

## Links

- [AASP Documentation](https://aasp-mvp.aminereg.com/docs)
- [AASP Dashboard](https://aasp-mvp.aminereg.com/dashboard)
- [AASP SDK](https://github.com/Amineregayeg/aasp-sdk)
