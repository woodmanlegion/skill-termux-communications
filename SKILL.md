---
name: "termux-communications"
description: "Termux communication and device control APIs: SMS, calls, contacts, notifications, sensors, with MMS workarounds and root capabilities."
metadata:
  {
    "openclaw":
      {
        "emoji": "📱",
        "requires": { "bins": ["termux-sms-send", "termux-sms-list", "termux-call-log", "termux-contact-list", "termux-telephony-call", "su"] },
        "platform": "android",
        "notes": "Requires termux-api package, paired Termux:API app, and root access for full MMS capabilities."
      },
  }
---

# termux-communications

Interact with Android device capabilities via Termux API. Enables SMS, calls, contacts, notifications, sensors, hardware control, and MMS workarounds on rooted devices.

## Prerequisites

- Install `termux-api` package: `pkg install termux-api`
- Install [Termux:API app](https://f-droid.org/packages/com.termux.api/) from F-Droid
- Pair the app with Termux (should auto-detect)
- **For MMS:** Root access required (`su` available)

## SMS (Text Messages)

### Send SMS
```bash
termux-sms-send -n "+1234567890" "Your message here"
```

### Read SMS (returns JSON)
```bash
# List recent messages
termux-sms-list -l 10

# Filter by type
termux-sms-list -l 5 -t inbox   # inbox only
termux-sms-list -l 5 -t sent    # sent only
```

**Output format:**
```json
[
  {
    "threadid": 123,
    "type": "inbox",
    "number": "+1234567890",
    "received": "2026-06-27 16:30:00",
    "body": "Message text..."
  }
]
```

## MMS (Media Messages) — Root Required

**Critical limitation:** `termux-api` has **no native MMS support**. Android blocks programmatic MMS sending. Use these workarounds:

### Workaround 1: Cloud Link Method (Recommended)
Upload image to cloud storage, send URL via SMS:
```bash
# Upload to Google Drive (make public), then:
termux-sms-send -n "+1234567890" "Image: https://drive.google.com/uc?id=FILE_ID"
```

### Workaround 2: mms-fetch Script (Root Required)
Fetch MMS attachments from Android's MMS database:
```bash
# Fetch latest MMS image
mms-fetch

# Fetch all MMS images
mms-fetch --all

# List available MMS parts
mms-fetch --list

# Fetch specific part by ID
mms-fetch --id 12345

# Fetch images newer than timestamp
mms-fetch --since 1750000000
```

**Output location:** `~/Downloads/mms/`

**Prerequisites for mms-fetch:**
- Root access (`su`)
- `sqlite3` binary available
- Script at `~/Scripts/mms-fetch`

### Workaround 3: Discord/Media Bridge
For images that need to reach a user:
1. Upload image to Discord channel (via Discord API)
2. Send Discord link via SMS
3. Or use Discord DM directly

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

### Send Image via Workaround Pipeline
```bash
# 1. Take screenshot or photo
screenshot --workspace

# 2. Upload to accessible location (e.g., Discord, Drive)
# 3. Send link via SMS
termux-sms-send -n "+1234567890" "Screenshot: $LINK"
```

### Monitor Battery and Notify
```bash
BATTERY=$(termux-battery-status | jq '.percentage')
if [ "$BATTERY" -lt 20 ]; then
  termux-notification --title "Low Battery" --content "${BATTERY}% remaining"
fi
```

### Auto-Reply to Recent SMS
```bash
# Get latest message
LATEST=$(termux-sms-list -l 1 -t inbox)
NUMBER=$(echo "$LATEST" | jq -r '.[0].number')
BODY=$(echo "$LATEST" | jq -r '.[0].body')

# Send auto-reply
termux-sms-send -n "$NUMBER" "Auto-reply: I'm busy, will respond later."
```

## Security Notes

- **SMS sending** is an external action — get user confirmation before sending
- **Call initiation** opens the dialer but requires user to press call
- **Root required for:** MMS fetching, low-level hardware access, some sensor modes
- **Treat as destructive:** These affect the real world (messages sent, calls made, notifications shown)
- **Privacy:** SMS/call logs contain sensitive data — handle carefully

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Permission denied" | Ensure Termux:API app is installed and has permissions |
| No output | Check that the app is paired (restart both if needed) |
| Commands hang | Some sensors/blocking reads need timeout handling |
| MMS fetch fails | Verify root (`su -c id`), check sqlite3 is installed |
| mms-fetch no results | Ensure MMS messages exist in Android Messages app |
| Camera fails | Check Termux:API has camera permission in Android settings |

## Related Resources

- `termux-api` package docs: https://wiki.termux.com/wiki/Termux:API
- TOOLS.md location: `~/.openclaw/workspace/TOOLS.md`
- mms-fetch script: `~/Scripts/mms-fetch`
