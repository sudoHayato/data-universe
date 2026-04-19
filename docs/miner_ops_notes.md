# Miner Operations Notes

Field notes from running a SN13 Data Universe miner in production for ~2 months. Complement to the main README.

## Release watching

We poll the upstream release tag via `watch_release.sh` on a 30-minute cron. When a new tag appears, `pm2 restart sn13-miner` picks up the updated code on next launch. This avoided us missing a critical `dynamic_desirability` config change that upstream shipped silently.

## Dynamic desirability sync cadence

Our cron runs `sync_dd_to_config.py` every 6 hours. Below that interval, validator-side scoring catches the shift before our miner reconfigures. Above 6 hours, we drift from the curated distribution. 6h was the stable point for our stake size; larger miners may need shorter.

## Apify token rate budget

A single miner hitting X/Twitter via Apify consistently runs into monthly rate caps well before month-end on standard Apify plans. Split your scraping across multiple Apify tokens if you're running above SN13 minimum validator expectations.

## Reddit PRAW vs Apify Reddit

PRAW (`reddit_custom_scraper`) is cheaper per-request but ratelimited at the Reddit API level, which caps effective throughput below what the subnet reward function expects. Apify Reddit is more expensive but hits higher sustained throughput.
