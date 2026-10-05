# Kabu strategy guide

How to build, test and race strategies in Kabu (v4). The same guide is in the app: Strategies → Strategy guide.

## What a strategy is in Kabu

A strategy is a set of rules Kabu checks on every stock in your watchlist **after each ASX close**. When a rule says buy or sell, the order fills at the **next morning's open**, with your brokerage and slippage from Settings. Kabu never looks at prices from the future, and never trades during the day.

There are two kinds you can build:

- **Buy & sell rules**: buy a stock when *all* your buy rules are true; sell it when *any* sell rule (or a safety exit) is true. Each stock gets an equal share of the money.
- **Monthly rotation**: on the first trading day of each month, hold the best few stocks by recent return, and sell anything that drops out.

Every switched-on strategy is backtested (Strategies → Compare), paper-traded by the bot each evening once paper trading has started, emailed to you, and shown on the Home leaderboard against you and the ASX 200.

## Build your first strategy (2 minutes)

1. Go to **Strategies** and tap **New strategy**.
2. Pick a starting point, e.g. **RSI pullback**. Every preset can be changed.
3. Change a number, e.g. the RSI level from 10 to 5. The **Backtest** at the bottom recalculates as you type, next to the ASX 200.
4. Read **In plain English**: it is exactly what the bot will do.
5. Tap **Save strategy**. It appears in Compare and in your list, switched on.
6. If paper trading is running, the bot trades it from the next close. Its trades and orders show on Home and in the evening email.

To change it later, tap it in the list, then **Edit**. **Duplicate** makes a copy to try a variation side by side. The switch turns it off without deleting it.

## Buy rules (all must be true)

Each rule is checked on the day's closing price. Combine two or three: one for the **trend** (is this stock going up at all?), one for the **moment** (is now a good entry?), and optionally the **market filter**.

| Rule | What it measures | Typical settings |
|---|---|---|
| Price vs its average | Where the close is relative to the average close of the last N days. "Above its 200-day average" is the classic definition of a long-term uptrend. | 200 days (long trend), 50 (medium), 10 or 5 (short). A buffer like 2% avoids flip-flopping. |
| Average vs average | A faster average against a slower one. **is above** is true every day while it lasts; **crosses above** is true only on the day it happens. | 50 vs 200 ("golden cross"), 20 vs 50, 10 vs 30 |
| RSI | Relative Strength Index, 0 to 100: how one-sided recent moves have been. Low means sharply sold off, high means sharply bid up. | 2-day RSI below 10 (very short-term dip), 14-day RSI below 30 (classic oversold) |
| Drop from a recent high | How far the close is below the highest price of the last N days. | 7% from the 20-day high (what Dip buying uses) |
| New high or low | The close is above the highest close of the previous N days (a breakout), or below the lowest (a breakdown). | Buy a new 55-day high; sell a new 20-day low |
| Return over a period | Percentage change over the last N trading days. Historically, strong recent returns have often carried on for a while (the "momentum" effect), but not reliably; a strongly negative return can be a falling knife. | 126 days ≈ 6 months, 252 ≈ 1 year; above 0% |
| Whole market | Whether the ASX 200 fund (your benchmark) is above its N-day average. Use it to stay out of bear markets. | ASX 200 above its 200-day average |

> Rules use Yahoo Finance's daily closing prices, which are adjusted for share splits. Dividends are paid to you in cash on the ex-date; franking credits are not counted.

## Sell rules (any one is enough) and safety exits

Sell rules use the same list as buy rules: typically the opposite of your trend rule (e.g. "price closes **below** its 200-day average") or a short-term target (e.g. "price closes above its 5-day average" for a dip-buying strategy).

Safety exits are simpler and work on every position:

- **Stop-loss**: sell if the close is X% below the price you paid. Limits the damage of one bad trade.
- **Trailing stop**: sell if the close is X% below the best close since you bought. Locks in part of a big gain.
- **Take profit**: sell if the close is X% above the price you paid.
- **Sell after N days**: a time limit, for strategies that expect a quick bounce.

> All exits are checked at the close and filled at the next open, like a person checking prices each evening. If a stock opens much lower (bad news overnight), the sale happens at that lower price, so a loss can end up bigger than the stop-loss.

If you set no sell rules and no safety exits, the strategy never sells: it slowly turns into buy-and-hold.

## Money and positions

- **Equal shares**: each stock gets the same slice of the strategy's money. With 12 stocks on the watchlist and no limit, each slice is 1/12.
- **Hold at most N stocks**: bigger slices (1/N each). When more stocks qualify than there is room for, the rest wait.
- **If there are more buys than room**: take them in watchlist order, the strongest 6-month return first, or the most oversold (lowest 14-day RSI) first.
- **Wait before buying again (cool-down)**: after selling a stock, skip it for N trading days. Without this, a strategy can sell on a stop-loss and buy straight back the next day.

Costs come from Settings and apply to every strategy and to you. With small amounts the **minimum fee** dominates: on a $500 trade a $5 fee is 1% each way. Strategies that trade often pay this again and again; the Compare table shows total fees.

## Monthly rotation

Once a month (first trading day), Kabu ranks every watchlist stock by its return over the last N days and keeps the top few in equal amounts. Stocks that fall out of the top are sold; new ones are bought. Optional: only buy stocks whose return is above a minimum, and sit in cash while the ASX 200 is below its long average.

This is a momentum strategy. It trades at most once a month per stock, so fees stay low, but it can hold the same few names through sharp drops until the next month.

## The ready-made strategies

| Preset | Idea | What to watch |
|---|---|---|
| Golden cross | Own stocks while their 50-day average is above their 200-day average. | Few trades and slow to react: it gives back part of a gain before selling. |
| RSI pullback | In an uptrend, buy after a sharp short drop and sell on the bounce. Stop-loss 8%, max 10 days, 5-day cool-down. | Many short trades, so fees matter. Works when stocks mean-revert; hurts in a crash. |
| Breakout | Buy new 55-day highs, sell new 20-day lows or 15% below the best close. | Many small losses and a few big winners. Expect a low win rate. |
| Momentum rotation | Each month, hold the 3 best 6-month performers while the ASX 200 is in an uptrend. | Concentrated: 3 stocks. Look at the worst fall, not just the return. |
| Trend + market filter | The built-in Trend filter, plus: only while the whole market is above its 200-day average. | Spends more time in cash. Compare its worst fall with the plain Trend filter. |

These are well-known rule families, chosen as starting points. None of them is a recommendation; test them on several periods before trusting any.

## Testing a strategy properly

- **Look at several periods** (1Y, 3Y, 5Y, 10Y). A rule that only wins in one period probably got lucky.
- **Look at the worst fall**, not just the return. Could you sit through it with real money?
- **Check trades and fees.** A strategy with hundreds of trades a year on a small account is mostly paying brokerage.
- **Compare with the ASX 200.** Doing nothing (buying the index) is the bar to beat, and many active strategies fall short of it after costs.
- **Don't tune to perfection.** If you change numbers until the backtest looks great, you have fitted the past, not found a rule. Prefer round, sensible numbers and rules that work across periods.
- **Then paper-trade it.** Paper results come from prices nobody had seen when you saved the rule. A few months of paper trading tells you more than any backtest.

> Limits of the backtest: the watchlist is today's big companies, so stocks that shrank or disappeared over 10 years are missing (this flatters every strategy). The "10Y" view starts about 10 months after the first price, because 200-day averages need that history. Prices come from Yahoo Finance and can be late or wrong.

## Racing the bot with your own trades

The **Trade** tab is your own paper account, with the same starting money and costs as the bot. Buy by dollar amount or number of shares; sell some or all. Any ASX stock works, not just the watchlist.

- Your order fills at the **first open after you place it** (10:00 Sydney). Placed before 10:00 on a weekday: that morning. Placed during or after trading, or at the weekend: the next trading morning. The bridge stamps the time, so an order can't be back-dated.
- You can **cancel** until 10:00 on the day it fills.
- If you don't have enough cash, a buy fills as many whole shares as you can afford, or is skipped (shown under "Couldn't fill").
- The **Home leaderboard** ranks you against every strategy and the ASX 200 since paper trading started. Restarting paper trading restarts everyone, including you.

A fair test: decide your rule first, write it down, then trade it by hand for a month. If you keep beating the bot, build your rule in the strategy builder and let the bot run it too.

## Worked examples

**1. Buy dips, but only in a rising market.**

1. New strategy → **Start from scratch**. Name it "Dips in uptrends".
2. Buy rule 1: Price vs its average → price closes **above** its **200**-day average by **0**%.
3. Add buy rule 2: Drop from a recent high → fallen **5**% or more from its **10**-day high.
4. Add buy rule 3: Whole market → ASX 200 fund **above** its **200**-day average.
5. Sell rule: Price vs its average → price closes **above** its **10**-day average. Safety exits: stop-loss **8**, sell after **15** days. Cool-down **5** days.
6. Check the backtest on 3Y and 10Y, then Save.

**2. Hold the strongest three, step aside in bear markets.**

1. New strategy → **Momentum rotation**.
2. Hold **3**, ranked by return over **126** days, only if above **0**%, only while the ASX 200 is above its **200**-day average.
3. Try 252 days instead of 126 with Duplicate, and keep both switched on for a while to see which holds up on paper.

## Limits and good to know

- Up to **8** strategies of your own, each with up to **6** buy and **6** sell rules.
- The bot's strategies trade only the watchlist (Stocks tab). Changing the watchlist, money, costs or a strategy recalculates its results from the start, including paper results.
- The bot runs once each weekday evening (about 5–6 pm Sydney). **Run now** on Home runs it immediately.
- Your strategies live in your bridge (Google Drive), so they survive a reinstall or a new phone.
- Paper money only. Not financial advice.
