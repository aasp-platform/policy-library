# CLAUDE.md

This file provides context to AI coding assistants (Claude Code, Cursor, etc.) when working with this codebase.

## Project Overview

AASP Policy Library is a collection of production-ready security policies for the AI Agent Security Platform. Users can copy, customize, and deploy these policies to secure their AI agents.

## Structure

```
policies/
├── security/           # Attack prevention, data protection
├── compliance/         # SOC2, GDPR, PCI-DSS, HIPAA
├── operations/         # Business logic, rate limiting
└── industry/           # Finance, healthcare, legal
```

## Policy Format

All policies are JSON files following this schema:

```json
{
  "name": "Policy Name",
  "description": "What this policy does and why it's important",
  "priority": 80,
  "enabled": true,
  "rules": [
    {
      "id": "unique-rule-id",
      "actionType": "tool_call",
      "targetPattern": "^regex_pattern$",
      "conditions": [
        {
          "field": "params.fieldName",
          "operator": "eq",
          "value": "matchValue"
        }
      ],
      "effect": "BLOCK"
    }
  ]
}
```

## Fields Reference

### Policy Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Human-readable policy name |
| `description` | string | Yes | Purpose and use case |
| `priority` | number | Yes | 1-100, higher = evaluated first |
| `enabled` | boolean | Yes | Whether policy is active |
| `rules` | array | Yes | List of rules to evaluate |

### Rule Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | Unique rule identifier |
| `actionType` | string | Yes | `tool_call`, `api_call`, `db_query`, `file_access` |
| `targetPattern` | string | Yes | Regex to match action target |
| `conditions` | array | No | Additional match conditions |
| `effect` | string | Yes | `ALLOW`, `BLOCK`, `REQUIRE_APPROVAL` |

### Condition Operators

| Operator | Description | Value Type |
|----------|-------------|------------|
| `eq` | Exact equality | any |
| `neq` | Not equal | any |
| `gt` | Greater than | number |
| `lt` | Less than | number |
| `gte` | Greater or equal | number |
| `lte` | Less or equal | number |
| `contains` | String contains | string |
| `matches` | Regex match | string (regex) |

## Common Tasks

### Creating a new policy

1. Identify the risk/action to control
2. Choose appropriate category directory
3. Create JSON file with descriptive name
4. Include comprehensive description
5. Test with AASP before submitting

### Testing a policy

```bash
# Import via API
curl -X POST https://aasp-mvp.aminereg.com/api/v1/policies \
  -H "x-api-key: aasp_test_xxx" \
  -H "Content-Type: application/json" \
  -d @policies/security/my-policy.json

# Trigger with test action
curl -X POST https://aasp-mvp.aminereg.com/api/v1/ingest \
  -H "x-api-key: aasp_test_xxx" \
  -H "Content-Type: application/json" \
  -d '{"agentId":"test","actionType":"tool_call","target":"test","params":{}}'
```

### Regex Tips

- `^` and `$` for exact match: `^send_email$`
- `.*` for wildcard: `.*_dangerous$`
- Escape special chars: `api\.stripe\.com`
- Case-insensitive: `(?i)drop|delete`

## Naming Conventions

- Files: `action-description.json` (kebab-case)
- Rule IDs: `category-action-description`
- Descriptive names over cryptic abbreviations

## Important Notes

- Policies are provided as starting points - customize for your needs
- Higher priority policies are evaluated first
- First matching rule wins (no fall-through)
- Test with `aasp_test_` keys before production
