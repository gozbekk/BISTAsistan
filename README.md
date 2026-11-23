<p align="center">
  <img src="screenshots/icon.jpg" width="96" alt="BISTAsistan icon">
</p>

<h1 align="center">BISTAsistan</h1>

<p align="center">
  <b>A simple, data-driven assistant for the Borsa Istanbul stock market.</b><br>
  Native iOS and Android apps to follow the BIST 100, track a portfolio, measure its risk and test a rules-based strategy with virtual money.
</p>

<p align="center">
  <a href="https://apps.apple.com/tr/app/bistasistan/id6797870700"><img src="https://img.shields.io/badge/App%20Store-available-black?logo=apple" alt="App Store"></a>
  <img src="https://img.shields.io/badge/Google%20Play-testing-green?logo=googleplay" alt="Google Play">
  <img src="https://img.shields.io/badge/iOS-Swift%20%2B%20SwiftUI-F05138?logo=swift&logoColor=white" alt="Swift">
  <img src="https://img.shields.io/badge/Android-Kotlin%20%2B%20Compose-7F52FF?logo=kotlin&logoColor=white" alt="Kotlin">
</p>

> **Note:** BISTAsistan is a commercial product, so its source code is private. This repository presents the project: what it does, how it is built, and what I learned. A detailed technical overview is available as a PDF: **[docs/bistasistan-overview.pdf](docs/bistasistan-overview.pdf)**.

---

## Screenshots

<p align="center">
  <img src="screenshots/02-market.jpg" width="19%" alt="Market overview">
  <img src="screenshots/03-stock-detail.jpg" width="19%" alt="Stock details and news">
  <img src="screenshots/04-portfolio.jpg" width="19%" alt="Portfolio and risk">
  <img src="screenshots/05-ai-simulation.jpg" width="19%" alt="AI virtual portfolio">
  <img src="screenshots/06-strategy.jpg" width="19%" alt="Strategy settings">
</p>

---

## The problem

Many new investors in Türkiye start trading on Borsa Istanbul without tools to understand what they buy. Prices, technical indicators, company announcements (KAP) and news are spread across many websites, and portfolio risk (for example, holding only banks) is rarely visible.

## The solution

BISTAsistan brings everything into one simple app:

- **Market at a glance**: BIST 100, market trend, top movers and news
- **Stock details**: charts from 1 day to 1 year, KAP announcements, news, and simple summaries of RSI, MACD and Bollinger Bands
- **Portfolio tracking**: value, profit and loss, sales history, and **portfolio risk** (sector concentration, volatility, drawdown scenarios)
- **Virtual portfolio simulation**: test a **rules-based algorithm** with virtual money, with no real trades
- **Budget analysis** and **notifications**: morning market summary, big market moves and price alerts

> ⚠️ BISTAsistan is an information and education app. It does not give investment advice.

---

## Features

| | Feature | Details |
|---|---|---|
| 📈 | **Market overview** | BIST 100 index, daily change, share of stocks in an upward trend, today's highlight, news |
| ⭐ | **Watchlists** | Four separate lists of followed stocks |
| 🔎 | **Stock details** | 1D–1Y charts, KAP announcements, Google News, technical indicators in plain language |
| 🔔 | **Price alerts** | Push notification when a stock reaches a chosen price |
| 💼 | **My Portfolio** | Holdings, live value, profit/loss, sales history, multi-currency display |
| 🛡️ | **Portfolio risk** | Sector concentration, volatility and possible drawdown scenarios |
| 🤖 | **AI virtual portfolio** | Rules-based strategy (cautious, balanced or aggressive) rebalanced monthly, with virtual capital |
| 🧪 | **Manual simulation** | Make your own virtual trades |
| 💰 | **Budget analysis** | Summary based on the user's budget and portfolio |
| 🌍 | **5 languages** | Turkish, English, French, Spanish, German |
| ⭐ | **Premium** | Monthly, 6-month and yearly plans with a 3-day free trial (Apple and Google in-app purchases) |

---

## Architecture

```mermaid
flowchart LR
    subgraph Clients
        I["📱 iOS app<br/>Swift + SwiftUI"]
        A["🤖 Android app<br/>Kotlin + Jetpack Compose"]
    end

    subgraph Backend["Backend (Vercel serverless)"]
        API["API routes<br/>stocks · signals · charts · news<br/>analysis · simulation · risk"]
        CRON["Scheduled jobs<br/>daily strategy · push notifications"]
    end

    DB[("Supabase<br/>auth · user data · subscriptions")]

    subgraph External
        MKT["Market data<br/>prices & history"]
        NEWS["KAP & news"]
        LLM["LLM API<br/>text summaries"]
        STORE["App Store & Google Play<br/>purchases · server notifications"]
        APNS["APNs<br/>push notifications"]
    end

    I --> API
    A --> API
    I --> DB
    A --> DB
    API --> DB
    API --> MKT
    API --> NEWS
    API --> LLM
    API --> STORE
    CRON --> APNS
    CRON --> API
```

- **Two native apps, one backend**: the iOS and Android apps share the same API and the same user accounts.
- **Numbers come from code, not from the AI.** Indicators (RSI, MACD, moving averages, Bollinger Bands), risk and the strategy are computed by deterministic code. The language model only writes short, readable summaries.
- **Server-side purchase verification** for both Apple and Google, and App Store server notifications to keep subscriptions up to date.

## Tech stack

| Layer | Technologies |
|---|---|
| iOS | Swift, SwiftUI, iOS 17+, StoreKit, APNs |
| Android | Kotlin, Jetpack Compose, Google Play Billing, WorkManager, Android 8+ |
| Backend | Node.js serverless functions on Vercel (Node and Edge runtimes), scheduled jobs |
| Database & auth | Supabase (PostgreSQL), Sign in with Google and e-mail |
| Research | Python backtesting scripts on daily price history |

---

## The strategy, and how I tested it

The virtual portfolio uses a **momentum strategy** that I designed and backtested in Python:

- every month, keep the **12 BIST stocks with the strongest 6-month momentum** (skipping the most recent month), only if their price is above the 200-day moving average;
- penalise very volatile stocks, and limit any sector to **25%** of the portfolio;
- the *cautious* mode moves to cash when the BIST 100 falls below its 200-day average.

To avoid overfitting, I tuned the parameters on one period and evaluated them on a **later, separate period**, and I checked that most of the ~200 parameter combinations gave similar results. I also documented the limits of the test (survivorship bias in today's stock list, returns in nominal Turkish lira). Details are in the PDF.

---

## What I learned

- Building and shipping **two native apps** (Swift and Kotlin) for the same product, on the App Store and Google Play
- Designing a **serverless backend** with scheduled jobs, push notifications and several external data sources
- **Quantitative research**: backtesting, train/test splits, robustness checks and honest reporting of limitations
- Making technical finance concepts (RSI, MACD, risk) **understandable for beginners**
- **Subscriptions** on both stores, with server-side verification
- Store rules for finance apps: clear disclaimers, no investment advice

---

<p align="center">
  Designed, built and published by <b>Görkem Özbek</b><br>
  <a href="https://apps.apple.com/tr/app/bistasistan/id6797870700">App Store</a>
</p>
