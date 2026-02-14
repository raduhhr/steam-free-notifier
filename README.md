# steam-free-notifier

Ansible role that deploys a self-hosted bot to notify a Discord channel whenever free-to-keep games appear on **Steam** or **Epic Games**.

![Discord embed example](https://img.shields.io/badge/Discord-webhook-5865F2?logo=discord&logoColor=white)

## Sources

| Source | Method | What it catches |
|--------|--------|-----------------|
| **Steam** | Search API + `appdetails` validation | Games discounted to -100% that were previously paid |
| **Epic Games** | `freeGamesPromotions` API | Weekly free game drops (paid→free promos only) |
| **GamerPower** | Aggregator API (supplementary) | Steam/Epic giveaways that direct scrapers might miss |

All sources filter out DLC, soundtracks, F2P titles, and other noise — only actual games that were paid and are now free get through.

## How it works

1. Cron runs `bot.py` once per hour
2. The script queries all three sources, deduplicates results
3. New games (not in `seen.json` cache) get sent as Discord embeds via webhook
4. Games are only marked as "seen" after Discord confirms delivery

## Requirements

- Python 3.8+
- `python3-requests`
- `python3-bs4` (BeautifulSoup4)
- A Discord webhook URL

## Ansible deployment

### Role variables

| Variable | Required | Description |
|----------|----------|-------------|
| `steam_free_notifier_webhook` | **yes** | Discord webhook URL |

### Example playbook

```yaml
- hosts: homelab
  roles:
    - role: steam-free-notifier
      vars:
        steam_free_notifier_webhook: "https://discord.com/api/webhooks/YOUR/WEBHOOK"
```

> **Tip:** Store the webhook in Ansible Vault rather than plaintext.

### What the role does

1. Installs `python3-requests` and `python3-bs4` via apt
2. Creates `/opt/steam-free-notifier/` and deploys `bot.py`
3. Installs a cron job under `/etc/cron.d/steam-free-notifier`

## Standalone usage (no Ansible)

```bash
# Install deps
sudo apt install python3-requests python3-bs4

# Copy the script
sudo mkdir -p /opt/steam-free-notifier
sudo cp files/bot.py /opt/steam-free-notifier/bot.py
sudo chmod +x /opt/steam-free-notifier/bot.py

# Test run
DISCORD_WEBHOOK="https://discord.com/api/webhooks/YOUR/WEBHOOK" \
  python3 /opt/steam-free-notifier/bot.py

# Add to crontab (runs hourly at :17)
echo "17 * * * * root DISCORD_WEBHOOK='YOUR_WEBHOOK' /usr/bin/python3 /opt/steam-free-notifier/bot.py >> /var/log/steam-free-notifier.log 2>&1" \
  | sudo tee /etc/cron.d/steam-free-notifier
```

## Discord output

The bot posts as **Captain Hook** with rich embeds including game images (Steam), platform icons, claim links, and expiry dates (Epic).

## License

MIT
