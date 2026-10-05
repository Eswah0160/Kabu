# Kabu 株

Kabu tests trading rules on real ASX prices. You can see what they would have done over the last ten years, then run them forward with **paper money**, build your own rules, and trade alongside the bot yourself to see who wins. Nothing is bought or sold for real.

| Strategy | The rule |
|---|---|
| **Trend filter** | Hold a stock while it closes more than 2% above its 200-day average. Sell when it closes more than 2% below it. |
| **Monthly invest** | Always invested, with equal amounts in every stock. On the first trading day of each month, add any monthly top-up and rebalance. |
| **Dip buying** | Buy a stock after it falls 7% from its 20-day high, but only while it's above its 200-day average. Sell when it bounces above its 10-day average, after 20 days, or at an 8% stop-loss. |

Each strategy is compared with buying and holding an ASX 200 fund (IOZ). You can switch strategies on and off, change their settings, and **build your own** from rules (golden cross, RSI pullbacks, breakouts, momentum rotation and more). How to do that is in **[STRATEGIES.md](STRATEGIES.md)**, the same guide that's in the app.

## What's in the app

- **Home** is a dashboard: whether the bot is running and when it runs next, what it did at the last open and will do at the next, a **leaderboard** of you vs every strategy vs the ASX 200, what is held right now, recent activity, and the market.
- **Strategies** compares everything over 1–10 years, switches strategies on and off, and builds new ones with a live backtest as you type.
- **Trade** is your own paper account: buy and sell any ASX stock. Your orders fill at the next open with the same costs as the bot, and you can cancel until then.
- **Stocks** is the watchlist the strategies trade. **Settings** holds the bridge, money and costs, and app updates.
- The version you're on is always at the top (e.g. **v3**). After an update, Home says **Updated to Kabu v3 ✓** and what's new.

Kabu comes in two forms that share this website:

- **The Android app** (recommended on the Fold). It keeps its data inside the app, not in Chrome, and updates itself from this website. Its setup is in the `kabu-android` repository's README.
- **The web app**, which you open from this website in any browser.

## Set up the website (once)

Kabu needs its own repository. Don't put it inside Kura's.

1. On GitHub, create a new repository called `kabu`. Make it **Public**, because Pages needs that on a free account. There are no secrets in these files.
2. Click **uploading an existing file**. Drag in everything from this folder: `index.html`, `version.json`, `sw.js`, `manifest.webmanifest`, the four icons, `STRATEGIES.md` and this README. Then click **Commit changes**.
3. Go to **Settings → Pages**. Under Source choose **Deploy from a branch**, pick `main` and `/ (root)`, then **Save**.
4. After a minute the site is live at `https://YOURNAME.github.io/kabu/`.

If you already have a `kabu` repository, upload these files over the old ones instead (always `index.html` and `version.json` together).

## Connect the bridge

The bridge is a small Google Apps Script in your own Google account. It loads ASX prices from Yahoo Finance and runs the paper bot after every close. It is a **separate** project from Kura's bridge.

1. In Kabu, go to **Settings → Copy bridge script**.
2. On a computer, open **script.google.com**, start a **New project**, paste the script over everything, and save.
3. Pick **setupKabu** in the function menu and press **Run**.
   - Google asks for permission. If it warns you, choose **Advanced → Go to project**.
   - This switches on the daily bot.
4. Click **Deploy → New deployment** and set the type to **Web app**.
   - Execute as: **Me**. Who has access: **Anyone**.
   - Click **Deploy** and copy the URL that ends in `/exec`.
5. In Kabu, paste the URL and tap **Save and test**. The first load of 10 years of prices takes about half a minute.

**Already have a bridge (v1 or v2)?** Keep it, and update it in place, so the URL and your paper trading stay the same. Kabu v3 needs bridge v3 for your own orders, strategies you build and the dashboard's activity log:

1. In Kabu go to **Settings → Bridge**. If this phone or app doesn't know your bridge yet, open **Already have a bridge?**, paste its key (line 3 of the script in Apps Script: `var SECRET = '…'`) and the `/exec` URL, and tap **Save and test**.
2. Home then shows **Bridge update**. Tap **Copy bridge script**, paste it over all the old code in Apps Script, and save.
3. Click **Deploy → Manage deployments**, tap the pencil, set Version to **New version**, then click **Deploy**. Tap refresh in Kabu: the box disappears once the bridge reports v3.

## Compare, then start paper trading

1. **Strategies → Compare** shows each strategy over 1, 3, 5 or 10 years, with returns, the worst fall, the number of trades and fees. The "10Y" view starts about 10 months after the first price, because the 200-day averages need that much history first.
2. Build your own if you like: **Strategies → New strategy** (see [STRATEGIES.md](STRATEGIES.md)).
3. When you're ready, tap **Start paper trading**. From then on, the bot runs after each ASX close (about 5–6pm Sydney time, weekdays) for every switched-on strategy, and your own account opens with the same money.
4. Place your own paper trades on the **Trade** tab, and watch the **leaderboard** on Home.
5. The bot emails you whenever a paper trade was made (by it or you) or one is due at the next open.

## Releasing a new version

A new version of Kabu is two files: `index.html` and `version.json`. Always upload **both together** to this repository.

- **The Android app** finds the new version within about 6 hours, or straight away from **Settings → App updates → Check for updates**. It checks that the download matches `version.json` exactly, then asks you to **Restart to update**. If the new version doesn't start, the app goes back to the old one by itself and skips that version.
- **The web app** picks up the new `index.html` the next time it's opened online.
- If the bridge script changed too, Kabu shows **Bridge update** on Home with the steps.

## Good to know

- **Orders fill at the next morning's open**, the bot's and yours. Each order also pays the brokerage and slippage you set in Settings. Only whole shares are traded. Dividends are paid in cash on the ex-date; franking credits are not counted. Your order's time comes from the bridge, so it can't be back-dated.
- **Paper results are recalculated from the start date every time.** Changing the rules or the stock list changes the paper results too. Restarting begins again from today.
- **Prices come from Yahoo Finance's unofficial chart API, through your bridge.** Yahoo limits how often it can be asked. If loading fails, wait a few minutes and tap refresh. If a stock couldn't be refreshed, Kabu says so and shows the last prices it has.
- **Your settings live in the bridge** (a Kabu folder in your Google Drive), so a new phone or a reinstall only needs the bridge URL and key.
- **Not financial advice.** Most active traders do worse than simply holding an index fund. Backtests flatter strategies that look good on past prices. Treat Kabu as a way to learn what a rule really does before trusting it with money.

## Later stages

- **Approve each trade.** The bot proposes an order and you tap **Approve** before it is placed.
- **Automatic trading through Interactive Brokers' API.** Only after paper trading has earned it, and with hard limits, a kill switch and signed app updates.
