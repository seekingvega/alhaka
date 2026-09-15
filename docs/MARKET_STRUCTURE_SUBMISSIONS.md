# Market-structure ideas in the hackathon field

A follow-up to [HACKATHON_SUBMISSIONS.md](HACKATHON_SUBMISSIONS.md) answering one
question: which of the 427 Alpaca AI Trading Agents Hackathon submissions use
market-structure concepts such as volume profile, order flow, dealer positioning or
auction theory? Method: a keyword scan over every project's lablab summary on
2026-09-09, then a read of the matching sentences to drop false positives
("tape" as audit tape, "sweep" as parameter sweep, "POC" as proof of concept).
Summaries only; repositories and presentations were not opened.

Contents: [Headline](#headline) · [Dealer gamma positioning](#dealer-gamma-positioning) ·
[Smart-money price structure](#smart-money-price-structure) ·
[Options order flow](#options-order-flow) · [Auction and level-based ideas](#auction-and-level-based-ideas) ·
[Loose use of the words](#loose-use-of-the-words) · [What this means for PACA](#what-this-means-for-paca) ·
[Caveats](#caveats)

## Headline

Market structure is rare in this field. About a dozen of 427 projects use it in
earnest, and the classic intraday toolkit is absent: **no submission mentions volume
profile, TPO, value area, footprint charts or cumulative delta.** What does appear
clusters into four ideas, in decreasing order of how often it was the actual edge:

| Idea | Projects | Strongest example |
|---|---|---|
| Dealer gamma positioning (GEX, gamma walls, flip point) | 4 | Pin Desk |
| Smart-money price structure (FVG, order blocks, liquidity sweeps) | 4 | FVG Copilot |
| Options order flow (unusual volume, sweeps, blocks) | 4 | Alpaca MCP Options Flow Agent |
| Auction theory, VWAP and gaps as levels, order book | 6 | Augur, Apex Cypher |

## Dealer gamma positioning

The most common genuine market-structure edge, and the one computed entirely from
data Alpaca already provides (the options chain and open interest).

- **[Pin Desk](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/gasbin/pin-desk)** — Gasbin · 1 vote  
  Does not predict direction. Computes net dealer gamma across the live chain from
  open interest, finds the strike where it flips from positive to negative, and sells
  defined-risk iron condors around the pin only while dealers are long gamma and
  damping price. When the regime flips, the desk stands down. The purest
  market-structure thesis in the field.
- **[Worca](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/yvl/worca-an-agentic-swarm-to-automate-trading)** — yvl · 0 votes  
  A "Gamma Scout" agent computes signed gamma exposure per strike to locate dealer
  gamma walls and the flip point, paired with a "Vol Surface Surfer" tracking the
  front-to-back IV term structure.
- **[MSAR_HMM_Gamma_Trend_Strategy](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/wall-street-quant/msarhmmgammatrendstrategy)** — Wall Street Quant · 1 vote  
  Dealer gamma from Alpaca open interest is one of three regime gates, alongside a
  Markov volatility filter and a 200-day trend veto. Long or flat only.
- **[VolHelix AI](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/trojanxode/volhelix-ai-multi-agent-options-trading-swarm)** — TrojanXode · 1 vote  
  Entries must pass an "Order Flow Confluence Gate" scoring GEX call and put walls
  together with order blocks and fair value gaps at 70 percent or higher.

## Smart-money price structure

ICT-style concepts: fair value gaps (FVG), market-structure shifts, order blocks,
liquidity sweeps and absorption. Retail-popular, rarely quantified.

- **[FVG Copilot](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/teamhobbsian/fvg-copilot-multi-agent-options-trading-on-alpaca)** — TeamHobbsian · 1 vote  
  Ports a production FVG plus market-structure-shift plus displacement equity
  strategy, claimed to be live on real accounts, to options on SPY, QQQ, XLV, XLF and
  IWM. A scout finds setups with the same deterministic core as the production bot,
  then builds candidate options trades. The only entry claiming a live-deployed origin.
- **[TEDA — SMV Options Alpha Agent](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/gedene/teda-smv-options-alpha-agent)** — Gedene · 0 votes  
  Scans ten liquid names every five minutes for institutional order-flow patterns,
  liquidity sweeps and volatility contraction, then structures an options trade.
- **[ADQuant](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/adquant/adquant-autonomous-agentic-options-trading-desk)** — ADQuant · 0 votes  
  One of four strategies is "Liquidity Sweep Absorption", scanned across 30-minute to
  daily timeframes on 500 assets, beside trend pullback, lead-lag and RSI reversal.
- VolHelix AI (above) combines order blocks and FVGs with gamma walls.

## Options order flow

Unusual options activity as a signal. Notable because one team proved the flow data
is reachable on Alpaca's WebSocket stream.

- **[Alpaca MCP Options Flow Agent](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/import-alpha/alpaca-mcp-options-flow-agent)** — Import Alpha · 0 votes  
  A custom MCP server detects sweeps, block trades and volume spikes in real time
  from Alpaca's WebSocket stream and feeds a multi-agent follow-through trader.
- **[Signal Hunter](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/signal-hunter/signal-hunter)** — Signal Hunter · 2 votes  
  A mechanical scanner flags unusual options volume relative to open interest; an
  LLM then researches each candidate before any trade.
- **[Tradenum](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/tradenum/tradenum)** — TradeNum · 1 vote  
  Follows or fades unusual prints into directional credit spreads, on the premise
  that most options flow is noise.
- **[Convexity](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/convexity/convexity-the-agent-that-cannot-buy-a-wide-book)** — Convexity · 2 votes  
  A volume-breakout signal fires first; a screener then ranks nearest-expiry option
  flow and a risk gate re-reads the bid/ask at order time.

## Auction and level-based ideas

- **[Augur](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/augur/augur)** — Augur · 1 vote  
  Every five minutes the LLM interprets the session using Auction Market Theory and
  forms a falsifiable directional thesis, then holds or buys a qualifying call or
  put. The only explicit auction-theory entry.
- **[Apex Cypher](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/apex-cypher/apex-cypher)** — APEX CYPHER · 1 vote  
  Equities, not options: enters long when an unfilled gap forms above VWAP, stop at
  VWAP plus or minus one ATR, two percent risk per trade, three losses a day and it
  stops. The closest analogue to PACA's gap event, with VWAP as the reference level.
- **[Eventus Algorithm](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/hivan04/eventus-algorithm)** — hivan04 · 1 vote  
  An intraday sleeve buys a front-expiry option on a VWAP trigger, flat by 15:15 ET,
  beside a condor sleeve and an earnings sleeve.
- **[Berkshire Alpha](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/larp/berkshire-alpha)** — Larp · 1 vote  
  VWAP deviation and volume-weighted momentum are regime inputs beside IV/RV and
  25-delta skew.
- **[QuantNova](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/quantnova/quantnova)** — QuantNova · 3 votes  
  VWAP is one of eight factors in a market score.
- **[BARCO](https://lablab.ai/ai-hackathons/alpaca-ai-trading-agents-hackathon/guacalita/barco-alpaca-ai-trading-agent)** — GUACALITA · 0 votes  
  A CNN reads live order-book snapshots every second and outputs a buy/sell pressure
  score, but on crypto.

## Loose use of the words

These matched the keywords but do not trade market structure:

- Maximus AI, ORACLE, RiskCourt and AlphaQuant AI say "market structure" and mean
  liquidity, spreads and open-interest checks before execution.
- Trade Guardian and The Bullish Bots show an order book on the dashboard; nothing
  trades off it.
- SENTINEL lists "market depth" among things an LLM can synthesize; no mechanism.
- Momentum Options Agent uses "order flow" to mean order routing through the CLI.

## What this means for PACA

PACA's entry is a momentum event (gap, breakout, MACD cross) gated by regime and
exhaustion checks, expressed as a debit vertical. None of the market-structure
projects above conflict with that; two could sit beside it as gates rather than
replace it.

- **Dealer gamma as a regime gate** (Pin Desk, MSAR_HMM). Net dealer gamma per
  strike is computable from the chain and open interest we already pull. Long-gamma
  regimes damp moves, which is exactly when a momentum breakout is least likely to
  follow through; a gate that lowers conviction or skips entries when the underlying
  sits inside a long-gamma pin would be a small, testable addition.
- **Gap plus VWAP** (Apex Cypher). Requiring the gap to hold above or below session
  VWAP before entry is a one-line filter on data we have.

Both would be **methodology changes** under `CLAUDE.md` and need explicit approval
and a study like [TAKE_PROFIT.md](TAKE_PROFIT.md) before shipping. Nothing here is
adopted; this page records what exists.

## Caveats

- Keyword scan over 2,000-character marketing summaries. A team that uses these ideas
  without naming them is missed; a team that names them may not have built them.
- Vote counts are lablab community likes, not judging results. Every project in the
  four idea sections has three votes or fewer, so the field did not reward market
  structure with attention either.
- Each project's primary category is in [HACKATHON_SUBMISSIONS.md](HACKATHON_SUBMISSIONS.md);
  most of these sit under directional options or volatility relative value.
