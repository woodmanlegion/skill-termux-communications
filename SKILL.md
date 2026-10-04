---
name: "termux-communications"
description: "Termux device control APIs: calls, contacts, notifications, sensors, media capture, system info. SMS/MMS moved to termux-sms — see AGENTS.md routing guide."
metadata:
  {
    "openclaw":
      {
        "emoji": "📱",
        "requires": { "bins": ["termux-call-log", "termux-contact-list", "termux-telephony-call"] },
        "platform": "android",
        "notes": "Requires termux-api package and a paired Termux:API app. SMS/MMS are handled by woodmanlegion/termux-sms, not this skill — see AGENTS.md.",
      },
  }
---

# termux-communications

Interact with Android device capabilities via Termux API: calls, contacts, notifications, sensors, hardware control, and media capture.

**SMS and MMS are not covered here** — use `sms-send`/`sms-receive`/`mms-send`/`mms-receive` from [`woodmanlegion/termux-sms`](https://github.com/woodmanlegion/termux-sms) instead (see `AGENTS.md`'s routing guide). This skill's own SMS/MMS sections (plain `termux-sms-send`/`termux-sms-list` calls, and a set of pre-root-MMS workarounds — cloud-link, a `~/Scripts/mms-fetch` script, a Discord bridge) were removed 2026-10-04: superseded by `termux-sms`, and in MMS's case actively inferior to it (real MMSC send/receive vs. workarounds for not having that).

## Prerequisites

- Install `termux-api` package: `pkg install termux-api`
- Install [Termux:API app](https://f-droid.org/packages/com.termux.api/) from F-Droid
- Pair the app with Termux (should auto-detect)

## Calls

### View Call Log
```bash
termux-call-log
```

### Initiate Call (dialer opens)
```bash
termux-telephony-call "+1234567890"
```

## Contacts

### List Contacts
```bash
termux-contact-list
```

**Filter example:**
```bash
termux-contact-list | jq '.[] | select(.name | contains("John"))'
```

## Device Control

### Notifications
```bash
# Send notification
termux-notification --title "Alert" --content "Something happened"

# Remove notification
termux-notification-remove <id>

# List notifications (Android 11+)
termux-notification-list
```

### Hardware
```bash
# Torch/flashlight
termux-torch on
termux-torch off

# Vibrate (milliseconds)
termux-vibrate -d 500

# Clipboard
termux-clipboard-get    # read
termux-clipboard-set    # write (pipe text)

# Brightness (0-255)
termux-brightness 128

# Volume (stream level)
termux-volume music 15
```

### Media Capture
```bash
# Take photo
termux-camera-photo ~/Pictures/photo.jpg

# Record audio (seconds)
termux-microphone-record -d 10 ~/audio.mp3

# Text-to-speech
termux-tts-speak "Hello from Termux"

# Speech-to-text (interactive)
termux-speech-to-text
```

### Sensors & Location
```bash
# GPS location (may need GPS enabled)
termux-location

# List available sensors
termux-sensor -l

# Read sensor (single sample)
termux-sensor -s accelerometer -n 1

# Read with delay
termux-sensor -s gyroscope -n 5 -d 100
```

### System Info
```bash
# Battery status (JSON)
termux-battery-status

# WiFi connection info
termux-wifi-connectioninfo

# WiFi scan
termux-wifi-scaninfo

# Cell info
termux-telephony-cellinfo

# Device info
termux-telephony-deviceinfo
```

## Advanced Patterns

### Monitor Battery and Notify
```bash
BATTERY=$(termux-battery-status | jq '.percentage')
if [ "$BATTERY" -lt 20 ]; then
  termux-notification --title "Low Battery" --content "${BATTERY}% remaining"
fi
```

## Security Notes

- **Call initiation** opens the dialer but requires user to press call
- **Treat as destructive:** these affect the real world (calls made, notifications shown)
- **Privacy:** call logs and contacts contain sensitive data — handle carefully

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Permission denied" | Ensure Termux:API app is installed and has permissions |
| No output | Check that the app is paired (restart both if needed) |
| Commands hang | Some sensors/blocking reads need timeout handling |
| Camera fails | Check Termux:API has camera permission in Android settings |

## Related Resources

- `termux-api` package docs: https://wiki.termux.com/wiki/Termux:API
- TOOLS.md location: `~/.openclaw/workspace/TOOLS.md`
- SMS/MMS: [`woodmanlegion/termux-sms`](https://github.com/woodmanlegion/termux-sms)
