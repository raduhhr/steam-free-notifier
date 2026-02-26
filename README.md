# steam-free-notifier

A small self-hosted Python bot that checks for **free-to-keep games** on **Steam** and **Epic Games**, then posts them to a Discord channel via webhook.

I made this mainly because I wanted something simple, local, and under my control. No bloated bot framework, no weird hosted service, no overengineering. Just a script that runs on a schedule, remembers what it already posted, and sends clean Discord embeds when something new shows up.

It currently checks:

- **Steam** — by looking for `-100%` discounts and validating them through app details
- **Epic Games** — through Epic’s promotions API
- **GamerPower** — as a supplementary source, but only official Steam/Epic links are accepted

## What it does

- scans supported sources for **paid → free** game promotions
- filters out obvious junk like DLCs, soundtracks, bundles, demos, etc.
- deduplicates results across sources
- posts only **new** finds to Discord
- keeps a local cache so it does not spam old entries again
- only accepts **official** store URLs for safety

## Why I made it

Wanted to see how scraping steam looks like and also how discord webhooks work. 


This one is meant to be:
- **self-hosted**
- **transparent**
- **easy to run from cron/systemd**
- **good enough for a homelab / personal server**

Not enterprise software. Not a package with 50 abstraction layers. Just honest work.

## Requirements

- Python **3.10+**
- `pip`
- a Discord webhook

## Install

Clone the repo and install dependencies:

```bash
git clone https://github.com/yourusername/steam-free-notifier.git
cd steam-free-notifier
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
