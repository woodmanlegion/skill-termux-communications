# AGENTS.md — termux-communications

Reference guide for the AI agent. This skill is a knowledge document —
it has no executable binary. Use it to understand what Termux API
capabilities are available on this device.

## When to consult this skill

- User asks what device capabilities are available via Termux
- Deciding which tool to use for SMS, MMS, calls, sensors, notifications
- Troubleshooting Termux API permission or pairing issues

## Routing guide

| Task | Use instead |
|------|-------------|
| Send plain text SMS | `sms-send` |
| Send MMS attachment | `mms-send` |
| Receive / read MMS | `mms-receive` |
| All other Termux API operations | Commands in SKILL.md |

## Framework Compatibility Notes

| Framework | Status | Notes |
|-----------|--------|-------|
| OpenClaw | ✅ Native | Reads `SKILL.md` + `AGENTS.md` |
| Pi | ✅ Compatible | Reads `SKILL.md` only |
