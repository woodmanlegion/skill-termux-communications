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
| Receive / read SMS | `sms-receive` |
| Send MMS attachment | `mms-send` |
| Receive / read MMS | `mms-receive` |
| All other Termux API operations (calls, contacts, notifications, sensors, media) | Commands in SKILL.md |

As of 2026-10-04: `SKILL.md`'s own SMS/MMS sections have been removed —
they're not just redundant now, this routing guide already pointed away
from them before `termux-sms` existed under these names. See
[`woodmanlegion/termux-sms`](https://github.com/woodmanlegion/termux-sms).

## Framework Compatibility Notes

| Framework | Status | Notes |
|-----------|--------|-------|
| OpenClaw | ✅ Native | Reads `SKILL.md` + `AGENTS.md` |
| Pi | ✅ Compatible | Reads `SKILL.md` only |
