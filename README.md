# Kabu 株

Kabu tests three simple trading rules on real ASX prices. You can see what they would have done over the last ten years, then run them forward with **paper money**. Nothing is bought or sold for real.

| Strategy | The rule |
|---|---|
| **Trend filter** | Hold a stock while it closes more than 2% above its 200-day average. Sell when it closes more than 2% below it. |
| **Monthly invest** | Always invested, with equal amounts in every stock. On the first trading day of each month, add any monthly top-up and rebalance. |
| **Dip buying** | Buy a stock after it falls 7% from its 20-day high, but only while it's above its 200-day average. Sell when it bounces above its 10-day average, after 20 days, or at an 8% stop-loss. |

Each strategy is compared with buying and holding an ASX 200 fund (IOZ). You can change the rules, the costs and the list of stocks in Settings.

Kabu comes in two forms that share this website:

- **The Android app** (recommended on the Fold). It keeps its data inside the app, not in Chrome, and updates itself from this website. Its setup is in the `kabu-android` repository's README.
- **The web app**, which you open from this website in any browser.

## Set up the website (once)

Kabu needs its own repository. Don't put it inside Kura's.

1. On GitHub, create a new repository called `kabu`. Make it **Public**, because Pages needs that on a free account. There are no secrets in these files.
2. Click **uploading an existing file**. Drag in everything from this folder: `index.html`, `version.json`, `sw.js`, `manifest.webmanifest`, the four icons and this README. Then click **Commit changes**.
3. Go to **Settings → Pages**. Under Source choose **Deploy from a branch**, pick `main` and `/ (root)`, then **Save**.
4. After a minute the site is live at `https://YOURNAME.github.io/kabu/`.

If you already have a v1 `kabu` repository, upload these files over the old ones instead.

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

**Already have a v1 bridge?** Keep it, and update it in place, so the URL and your paper trading stay the same:

1. In Kabu go to **Settings → Bridge**. Under **Already have a bridge?**, paste its key: it's on line 3 of the script in Apps Script (`var SECRET = '…'`). Paste the `/exec` URL too and tap **Save and test**.
2. Home then shows **Bridge update: v1 → v2**. Tap **Copy bridge script**, paste it over all the old code in Apps Script, and save.
3. Click **Deploy → Manage deployments**, tap the pencil, set Version to **New version**, then click **Deploy**.

## Compare, then start paper trading

1. **Compare** shows each strategy over 1, 3, 5 or 10 years, with returns, the worst fall, the number of trades and fees. The "10Y" view starts about 10 months after the first price, because the 200-day averages need that much history first.
2. When you're ready, tap **Start paper trading**. From then on, the bot runs after each ASX close (about 5–6pm Sydney time, weekdays).
3. It emails you whenever a paper trade was made or one is due at the next open.

## Releasing a new version

A new version of Kabu is two files: `index.html` and `version.json`. Always upload **both together** to this repository.

- **The Android app** finds the new version within about 6 hours, or straight away from **Settings → App updates → Check for updates**. It checks that the download matches `version.json` exactly, then asks you to **Restart to update**. If the new version doesn't start, the app goes back to the old one by itself and skips that version.
- **The web app** picks up the new `index.html` the next time it's opened online.
- If the bridge script changed too, Kabu shows **Bridge update** on Home with the steps.

## Good to know

- **Orders fill at the next morning's open.** Each order also pays the brokerage and slippage you set in Settings. Only whole shares are traded. Dividends are paid in cash on the ex-date; franking credits are not counted.
- **Paper results are recalculated from the start date every time.** Changing the rules or the stock list changes the paper results too. Restarting begins again from today.
- **Prices come from Yahoo Finance's unofficial chart API, through your bridge.** Yahoo limits how often it can be asked. If loading fails, wait a few minutes and tap refresh. If a stock couldn't be refreshed, Kabu says so and shows the last prices it has.
- **Your settings live in the bridge** (a Kabu folder in your Google Drive), so a new phone or a reinstall only needs the bridge URL and key.
- **Not financial advice.** Most active traders do worse than simply holding an index fund. Backtests flatter strategies that look good on past prices. Treat Kabu as a way to learn what a rule really does before trusting it with money.

## Later stages

- **Approve each trade.** The bot proposes an order and you tap **Approve** before it is placed.
- **Automatic trading through Interactive Brokers' API.** Only after paper trading has earned it, and with hard limits, a kill switch and signed app updates.
