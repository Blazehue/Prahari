# Prahari — Parametric Income Protection for India's Quick-Commerce Workforce

> "Standard insurance thinks in days. Quick-commerce workers think in 30-minute slots."

**Guidewire DEVTrails 2026 | Persona: Quick Commerce (Zepto / Blinkit)**

*Prahari (प्रहरी) — Sanskrit/Hindi for "sentinel" or "guardian on watch." A system that watches over workers so they don't have to watch their income disappear.*

---

## Table of Contents

1. [Problem Statement](#1-problem-statement)
2. [Product Overview](#2-product-overview)
3. [Persona & Use Cases](#3-persona--use-cases)
4. [System Architecture](#4-system-architecture)
5. [AI/ML Pipeline](#5-aiml-pipeline)
6. [Parametric Trigger Engine](#6-parametric-trigger-engine)
7. [Anti-Fraud Architecture](#7-anti-fraud-architecture)
8. [Adversarial Defense & Anti-Spoofing Strategy](#8-adversarial-defense--anti-spoofing-strategy)
9. [Weekly Pricing Model](#9-weekly-pricing-model)
10. [Tech Stack](#10-tech-stack)
11. [Development Roadmap](#11-development-roadmap)
12. [Deliverables](#12-deliverables)
13. [Constraints & Exclusions](#13-constraints--exclusions)

---

## 1. Problem Statement

India's quick-commerce workforce — delivery partners operating for Zepto, Blinkit, and Swiggy Instamart — represents one of the most economically vulnerable segments of the gig economy. These workers execute 8–15 deliveries per hour within a hyper-local 2km dark-store radius, with income tied directly to continuous platform availability and outdoor working conditions.

Unlike food delivery partners who absorb disruptions across longer shifts, Q-commerce workers operate in ultra-high-frequency windows where even a 30-minute halt translates to measurable, compounding income loss.

**The Gap:**

| Disruption Type | Duration | Estimated Income Loss (per worker) |
|---|---|---|
| Heavy rainfall (>25mm/hr) | 1.5 – 3 hrs | Rs. 280 – Rs. 560 |
| Dark store power outage | 30 – 90 mins | Rs. 140 – Rs. 420 |
| Hyperlocal AQI spike (>300) | 1 – 2 hrs | Rs. 210 – Rs. 420 |
| Micro-flood (pin-code level) | 2 – 5 hrs | Rs. 420 – Rs. 1,050 |
| Surge zone freeze | 45 – 90 mins | Rs. 180 – Rs. 360 |

No existing insurance product in India operates at this time granularity. Existing parametric products cover daily or weekly income loss events at city scale — entirely misaligned with the 30-minute operational rhythm of a Q-commerce worker.

**Prahari is built to close this gap.**

---

## 2. Product Overview

Prahari is an AI-powered income intelligence and parametric protection platform designed exclusively for Q-commerce delivery partners. The product is deliberately framed not as an insurance purchase but as an **income management tool** — workers engage with it through a weekly earnings forecast, and coverage activation is a natural extension of that financial awareness.

### Core Differentiation

| Dimension | Conventional Parametric Insurance | Prahari |
|---|---|---|
| Payout granularity | Day-level | 30-minute micro-slots |
| Premium model | Monthly or fixed daily | Dynamic weekly, repriced every Monday |
| Trigger scope | City-wide weather events | Pin-code and dark-store specific |
| Claim initiation | Worker-initiated | Fully automated, zero-touch |
| Fraud defense | GPS check | Multi-signal Spoofing Probability Score (SPS) |
| Product framing | Insurance purchase | Weekly income forecast + protection layer |
| Pricing intelligence | Flat rate by tier | XGBoost model — 5 hyper-local risk variables |

### The Sunday Night Forecast

Every Sunday night, each enrolled worker receives a personalized **Weekly Income Forecast Card**:

```
Week of March 24 – 30  |  Indiranagar Zone, Bengaluru
-------------------------------------------------------
Projected Earnings         Rs. 4,400
Disruption Risk            38% (High — heavy rain forecast Thu night)
Earnings at Risk           Rs. 1,200
Recommended Coverage Tier  Shield Night

Activate coverage for Rs. 38 this week  [Activate]
-------------------------------------------------------
```

This reframes the product from "buy insurance" to "protect what you have already earned the right to earn."

---

## 3. Persona & Use Cases

### Primary Persona

**Ravi, 26 | Blinkit Delivery Partner | Indiranagar Dark Store, Bengaluru**

- Shift: 9pm – 3am (night shift, 6 days/week)
- Average monthly income: Rs. 18,000 – Rs. 22,000
- Operating radius: 2km around assigned dark store
- Dependency: 100% on platform order dispatch and outdoor riding conditions
- Current safety net: None

---

### Use Case 1 — Night Rain Trigger

```
Context   : Tuesday, 1:30am. Rainfall reaches 32mm/hr in pin code 560038 (Indiranagar).
            Platform order volume in Ravi's zone drops 80% within 15 minutes.

Prahari   : Trigger threshold breached. Shift declared active. SPS score: 12 (clean).
            Claim auto-initiated. Payout calculated: 1.5hrs x Rs. 207/hr x 95% = Rs. 295.

Outcome   : Rs. 295 credited to Ravi's UPI in 4 minutes. No action required from Ravi.
```

### Use Case 2 — Dark Store Blackout

```
Context   : Friday, 11:10pm. Power outage at Indiranagar dark store.
            Zero orders dispatched from store ID BLR-IND-04 for 42 consecutive minutes.

Prahari   : Dark store blackout trigger fires. Corroboration check: 11 of 14 workers
            at that store also have active policies with matching inactivity signals.
            Batch validated. Claim auto-approved.

Outcome   : Rs. 220 disbursed. Payout arrives before Ravi notices the store is back online.
```

### Use Case 3 — Hyperlocal AQI Spike

```
Context   : Saturday, 3pm. Construction fire near Domlur pushes AQI in a 2km radius
            to 347 (Severe category) for 55 continuous minutes.

Prahari   : AQI trigger threshold breached. Ravi's declared shift is active.
            Zone cross-reference confirms he operates in the affected radius.

Outcome   : Rs. 180 disbursed for 1-hour protection window.
```

---

## 4. System Architecture

### 4.1 High-Level Architecture

```
+------------------+        +----------------------+        +-------------------+
|                  |        |                      |        |                   |
|   Worker PWA     +------->+   Prahari Core API   +------->+  ML Services      |
|   (React/Next)   |        |   (Go / Gin)         |        |  (Python/FastAPI) |
|                  |        |                      |        |                   |
+------------------+        +----------+-----------+        +--------+----------+
                                        |                            |
                             +----------v-----------+      +---------v----------+
                             |                      |      |                    |
                             |   MongoDB Atlas      |      |  XGBoost Pricing   |
                             |   (Workers, Policies,|      |  Isolation Forest  |
                             |    Claims, Events)   |      |  LSTM Forecast     |
                             |                      |      |                    |
                             +----------+-----------+      +--------------------+
                                        |
                +----------+------------+------------+-----------+
                |           |                        |           |
    +-----------v--+  +-----v---------+  +-----------v--+  +----v----------+
    |              |  |               |  |              |  |               |
    | OpenWeather  |  | Mock Platform |  | Razorpay     |  | Admin         |
    | Map API      |  | API (Node.js) |  | Test / UPI   |  | Dashboard     |
    | (Weather/AQI)|  | (Order feeds) |  | Simulator    |  | (Next.js)     |
    |              |  |               |  |              |  |               |
    +--------------+  +---------------+  +--------------+  +---------------+
```

---

### 4.2 Claim Lifecycle Architecture

```
                     EXTERNAL TRIGGER EVENT
                     (Weather / AQI / Platform)
                              |
                              v
                  +---------------------+
                  |  Trigger Monitor    |  <-- Polls APIs every 5 minutes
                  |  (Go goroutines)    |      during active shift windows
                  +----------+----------+
                             |
                    Threshold breached?
                             |
                   +---------v---------+
                   |  Shift Validator  |  <-- Is any enrolled worker's
                   |                   |      declared shift currently active
                   |                   |      in the affected zone?
                   +---------+---------+
                             |
                   +---------v---------+
                   |  SPS Engine       |  <-- Spoofing Probability Score
                   |  (Anti-Spoofing)  |      calculated per worker
                   +---------+---------+
                             |
            +----------------+------------------+
            |                |                  |
       SPS < 65         SPS 65-85           SPS > 85
            |                |                  |
     Auto-Approve       Soft Hold           Hard Hold
            |                |                  |
     Payout Engine     UX Verify Flow      Appeal Queue
            |                |                  |
            v                v                  v
     Razorpay/UPI     Confirm + Release    Human Review
     Disbursement     or Human Review      (4hr SLA)
```

---

### 4.3 Data Flow Architecture

```
+------------------+     Device Signals      +----------------------+
| Worker Mobile    +------------------------>+                      |
| (GPS, Accel,     |                         |   SPS Scoring        |
|  Cell tower,     |     Order History       |   Engine             |
|  Battery, Screen)+<------------------------+                      |
+------------------+     (from Mock API)     +----------+-----------+
                                                        |
                                             SPS Score (0-100)
                                                        |
+------------------+     Trigger Signal      +----------v-----------+
| External APIs    +------------------------>+                      |
| - OpenWeatherMap |                         |   Claim Processor    |
| - AQI feed       |     Zone Validation     |   (Go service)       |
| - Mock Platform  +<------------------------+                      |
+------------------+                         +----------+-----------+
                                                        |
                                             Claim Record
                                                        |
+------------------+     Payout Instruction  +----------v-----------+
| Razorpay Test    +<------------------------+                      |
| UPI Simulator    |                         |   MongoDB Atlas      |
|                  |     Webhook Confirm     |   (Claims, Policies, |
+------------------+------------------------>+    Workers, Events)  |
                                             +----------------------+
```

---

## 5. AI/ML Pipeline

### 5.1 Weekly Premium Scoring Engine

The premium engine runs every Monday at 06:00 IST and generates a personalized weekly premium for each enrolled worker using a five-variable weighted risk model.

**Input Feature Matrix**

| Feature | Variable Name | Weight | Source |
|---|---|---|---|
| 7-day rainfall forecast at pin code | `pincode_rain_7d` | 0.30 | OpenWeatherMap Forecast API |
| Shift risk multiplier | `shift_risk` | 0.25 | Worker-declared shift pattern |
| Zone historical flood/AQI index | `zone_risk_index` | 0.20 | Pre-computed static dataset |
| Worker consistency score | `consistency_score` | 0.15 | Internal — weeks active / weeks enrolled |
| Dark store reliability index | `store_risk_index` | 0.10 | Mocked platform uptime history |

**Scoring Formula**

```python
risk_score = (
    0.30 * pincode_rain_7d       +
    0.25 * shift_risk            +
    0.20 * zone_risk_index       +
    0.15 * (1 - consistency_score) +
    0.10 * store_risk_index
)

weekly_premium = BASE_PREMIUM + (risk_score * PREMIUM_MULTIPLIER)
# Floor: Rs. 15 | Ceiling: Rs. 65
```

**Model:** XGBoost regression, trained on synthetic disruption and income-loss data across 12 Indian metros. Validation set drawn from IMD historical rainfall + CPCB AQI records.

---

### 5.2 Micro-Payout Calculator

```python
payout = disruption_duration_hrs * declared_hourly_rate * coverage_pct

# Example:
# 1.5hr rain halt | Rs. 220/hr declared rate | 95% coverage tier
# = 1.5 * 220 * 0.95 = Rs. 313.50  -->  rounded to Rs. 314
```

---

### 5.3 Predictive Disruption Alert Model

A secondary LSTM model trained on IMD rainfall time-series and CPCB AQI sequences generates 6-hour ahead disruption probability forecasts per zone. Workers receive a push notification:

> "High rain probability in your zone after 11pm tonight (71% confidence). Your Shield Night policy is active. Estimated earnings at risk: Rs. 480."

---

### 5.4 ML Model Summary

| Model | Type | Purpose | Training Data |
|---|---|---|---|
| Premium Scorer | XGBoost Regression | Weekly premium per worker | Synthetic — 12 cities, 3 years |
| Disruption Forecaster | LSTM (time-series) | 6-hour ahead disruption probability | IMD rainfall + CPCB AQI historical |
| Spoofing Detector | Isolation Forest | SPS anomaly scoring per claim | Synthetic device signal dataset |
| Ring Detector | DBSCAN Clustering | Coordinated fraud ring identification | Claim timestamp + IP + device fingerprint |

---

## 6. Parametric Trigger Engine

All five triggers are **stateless, automated, and verifiable**. No worker action is required to initiate a claim. Trigger evaluation runs every 5 minutes against all active declared shifts.

| # | Trigger Name | Activation Threshold | Duration Condition | Data Source |
|---|---|---|---|---|
| T1 | Night Rain | Rainfall > 25mm/hr at worker's pin code | Sustained 20+ minutes | OpenWeatherMap Current Weather API |
| T2 | Dark Store Blackout | 0 orders dispatched from assigned store ID | 30+ consecutive minutes | Mocked Platform Order Feed API |
| T3 | Hyperlocal AQI Spike | AQI > 300 (Severe) within 2km radius | 45+ consecutive minutes | OpenWeatherMap Air Pollution API |
| T4 | Micro-Flood | Rainfall accumulation > 60mm in 3 hours at pin code | Single rolling window | OpenWeatherMap + Pin-code Flood Index |
| T5 | Surge Zone Freeze | Platform surge multiplier = 0 during declared peak shift | 45+ consecutive minutes | Mocked Platform Pricing Feed API |

### Trigger Evaluation Flow

```
Every 5 minutes (during active shift windows only):

FOR EACH active worker policy:
    1. Fetch current environmental data for worker's pin code
    2. Check all 5 trigger conditions
    3. If any threshold breached:
         a. Validate shift is declared active
         b. Calculate disruption duration (start time to present)
         c. Pass to SPS Engine for spoofing validation
         d. If SPS < 65: auto-initiate claim
         e. If SPS 65-85: hold + send soft-verify notification
         f. If SPS > 85: hold + queue for human review
```

---

## 7. Anti-Fraud Architecture

### Layer 1 — Location Consistency Validation

Every active policy emits a passive GPS beacon check every 15 minutes during declared shift hours. The worker's location cluster is compared against their declared dark-store zone (2km radius). A mismatch flags the claim for review.

**Catches:** Workers claiming coverage while physically outside their declared zone; shared-account fraud where a single individual holds policies for multiple worker identities.

---

### Layer 2 — Temporal Anomaly Detection

An Isolation Forest model monitors individual claim patterns over rolling 4-week windows. Anomalous signatures that elevate a worker's fraud risk score:

| Pattern | Risk Signal |
|---|---|
| Claim filed within 90 seconds of trigger activation | Suspiciously fast — suggests pre-monitoring of thresholds |
| Claims filed on >85% of all qualifying trigger events | Statistical outlier for genuine worker behavior |
| Trigger threshold crossed by minimal margin (< 5%) on repeated claims | Possible threshold-gaming behavior |
| Shift declared active within 10 minutes of a public weather alert | Reactive enrollment pattern |

Claims from workers with elevated fraud risk scores are automatically routed to manual review. Workers are not notified of their score.

---

### Layer 3 — Cross-Worker Corroboration

When a dark store blackout or zone-level trigger fires, Prahari cross-references claim behavior across all workers assigned to that store or zone.

**Corroboration Logic:**

```
Total workers assigned to store: N
Workers with active policies: P
Workers filing claims in this event: C

Expected claim rate = C / P

IF C / P < 0.30 AND N > 10:
    Flag batch for review
    (Fewer than 30% of policy-holding workers filing during a zone-wide event
     is statistically anomalous — suggests isolated fabrication, not genuine disruption)

IF C / P > 0.70:
    Batch validated — consistent with a genuine disruption event
```

**Catches:** Isolated fake claims during genuine events that lack corroboration from co-workers; fabricated store blackout claims contradicted by the platform's own order feed.

---

## 8. Adversarial Defense & Anti-Spoofing Strategy

> Crisis Context: A 500-worker syndicate coordinated via Telegram exploited GPS-only verification on a competing beta platform. Workers used commercial spoofing applications to place themselves inside severe weather alert zones while remaining at home, draining the liquidity pool through mass fraudulent payouts. Basic GPS verification is insufficient. Prahari's response follows.

---

### 8.1 The Differentiation — Genuine Worker vs. Active Spoofer

A genuine Q-commerce worker caught in a disruption produces a **multi-modal corroborating signal profile** that a spoofed device operating from a static home location cannot replicate.

**Signal Profile Comparison**

| Signal Dimension | Genuine Worker | GPS Spoofer |
|---|---|---|
| GPS coordinate drift | Natural micro-drift, 50–300m radius over 30 mins | Perfectly static — spoofing apps emit fixed coordinates |
| Accelerometer cadence | Footstep and bike vibration patterns consistent with outdoor idle | Flatline — phone on a desk surface |
| Cell tower sequence | Handoffs between towers matching declared zone | Tower IDs match home location, not declared zone |
| Battery drain rate | Elevated — active navigation, screen-on in poor weather | Baseline idle drain |
| Screen activity | High — repeated app checks for order availability | Low or irregular |
| Order ping history | Worker was receiving order pings 60–90 mins before trigger | Zero order activity in run-up period |
| `mock_location` flag | Not set | Frequently set by commercial spoofing apps |

The ML Isolation Forest model aggregates these signals into a single **Spoofing Probability Score (SPS)** per claim event.

**SPS Thresholds and Outcomes**

| SPS Range | Classification | Automated Action |
|---|---|---|
| 0 – 64 | Clean | Auto-approve, payout initiated in under 4 minutes |
| 65 – 85 | Soft Hold | Worker notified via non-accusatory verification prompt |
| 86 – 100 | Hard Hold | Claim auto-rejected, full appeal flow opened, human review assigned |

---

### 8.2 The Data — Signal Set for Ring Detection

Prahari analyzes three signal classes to detect not just individual spoofers but coordinated syndicate activity.

**Class 1 — Device-Level Signals**

| Data Point | Collection Method | Fraud Indicator |
|---|---|---|
| `mock_location` API flag | Native Android/iOS API | Spoofing app actively running on device |
| Accelerometer / gyroscope cadence | Passive sensor sampling (15-min windows) | Static flatline inconsistent with outdoor work |
| Cell tower ID sequence | Network registration data | Tower IDs not matching declared GPS zone |
| Battery drain delta | Battery API polling | Drain rate inconsistent with active outdoor use |
| Screen-on duration | Foreground activity monitor | Minimal screen use inconsistent with active delivery work |

**Class 2 — Behavioral Signals**

| Data Point | Collection Method | Fraud Indicator |
|---|---|---|
| Order ping history | Mocked platform API feed | Zero orders in 90-min window before trigger |
| Shift declaration timing | Platform event log | Shift declared within minutes of public weather alert |
| Claim-to-trigger latency | Internal timestamp delta | Sub-90-second filing suggests automated claim scripting |
| Historical claim overlap rate | Rolling 4-week window | >85% claim rate on qualifying events is anomalous |

**Class 3 — Network-Level Ring Detection Signals**

| Data Point | Collection Method | Fraud Indicator |
|---|---|---|
| Claim timestamp clustering | Event log analysis | 50+ claims in same 3-minute window = coordinated behavior |
| Device fingerprint overlap | Device ID + user-agent hash | Syndicate members reusing same spoofing hardware/tool |
| Account registration wave | Enrollment timestamp analysis | 20+ accounts registered in same pin code within 48hrs |
| IP subnet clustering | Request metadata | Multiple claimants on same home WiFi or mobile hotspot |

**Syndicate Alert Threshold:**

If Prahari detects more than 15 claims originating from the same pin code within a 5-minute window during a single trigger event, an automated **Syndicate Alert** is raised. The entire batch is held pending cross-signal analysis. Individual workers in the batch are not penalized unless their personal SPS independently exceeds 85.

---

### 8.3 The UX Balance — Protecting Honest Workers from False Flags

The structural risk in any fraud detection system is collateral damage to legitimate claimants. A Blinkit partner experiencing a genuine network drop in a flooded zone may exhibit some signals that superficially resemble spoofing. Prahari's system is designed around a **Presumption of Legitimacy** — workers are not suspects until the signal weight is decisive.

**Claim Processing Flow by SPS Band**

```
Parametric trigger fires
          |
          v
  SPS Score calculated
          |
     +----|-----+--------+
     |    |              |
  0-64   65-85         86-100
     |    |              |
     |   Soft Hold      Hard Hold
     |    |              |
  Auto-   Non-accusatory  Auto-rejected
  approve push notification + appeal flow opened
     |    |              |
  Payout  Worker taps     Human reviewer
  in      once to         assigned (4hr SLA)
  4 mins  confirm         |
          |               Worker notified
          |               with appeal link
     Fresh 10-sec
     GPS + accel burst
          |
     +----+----+
     |         |
  Confirmed  Not confirmed
     |         |
  Full payout  Moves to
  + Rs.10      human review
  credit on    (4hr SLA)
  next premium
```

**Soft Hold Notification Copy (Worker-Facing)**

The notification deliberately avoids any language implying suspicion:

> "Your claim for the disruption (11:42pm – 1:18am) is being processed. In some cases, our system requests a quick location confirmation when network conditions are unstable during bad weather. Tap below — it takes 10 seconds and releases your payout immediately."

**Worker Protection Commitments**

| Commitment | Specification |
|---|---|
| Language policy | The word "fraud" is never used in worker-facing communications |
| Soft hold resolution SLA | 2 hours maximum — no worker waits overnight for a decision |
| Appeal turnaround | Human reviewer responds within 4 hours of appeal submission |
| Flag escalation threshold | Single anomalous claim does not raise a worker's base SPS; minimum 3 independent anomalies required |
| Compensation for delays | If a Soft Hold claim is ultimately approved, the worker receives full payout plus Rs. 10 credited against their next week's premium |

---

## 9. Weekly Pricing Model

### Coverage Tiers

| Tier | Weekly Premium | Coverage Cap | Target Worker Profile |
|---|---|---|---|
| Shield Basic | Rs. 15 – Rs. 25 | Rs. 800 / week | Day shift, low-risk zones, consistent history |
| Shield Plus | Rs. 26 – Rs. 45 | Rs. 1,800 / week | Mixed shift, moderate-risk zones |
| Shield Night | Rs. 46 – Rs. 65 | Rs. 3,200 / week | Night / weekend workers, high-risk zones |

Premium floor and ceiling are enforced at the model level. The AI-generated premium determines the worker's eligible tier range; the worker selects within that range. Policy is active Monday 00:00 IST through Sunday 23:59 IST.

### Why Weekly

Q-commerce platforms disburse worker earnings on a weekly cycle. Prahari's policy window is intentionally synchronized with this cycle — not as a design convenience, but as a deliberate alignment with how workers already think about their income. A monthly commitment creates an access barrier for workers with irregular earnings. A daily model creates friction at exactly the moment workers should be focused on working. Weekly is the natural unit.

### Premium Lifecycle

```
Sunday night    Monday 06:00    Monday 00:00     Sunday 23:59     Monday 06:00
     |                |               |                |                |
     v                v               v                v                v
Forecast Card    XGBoost model   Policy           Policy           Next cycle
shown to worker  re-runs for     activates        expires          begins
(with suggested  all workers     for enrolled                      premium
 premium + tier)                 workers                           recalculated
```

---

## 10. Tech Stack

### Component Map

| Component | Technology | Rationale |
|---|---|---|
| Worker PWA | React + Next.js 14 + TypeScript | Mobile-first, offline-capable for low-connectivity zones |
| Insurer Admin Portal | Next.js 14 + TypeScript + Tailwind CSS | Unified codebase with worker app |
| Core API | Go (Gin framework) | High concurrency for real-time trigger polling across all workers |
| AI/ML Services | Python (FastAPI) + XGBoost + scikit-learn | Established ML ecosystem; FastAPI for low-latency inference |
| Anti-Spoofing Engine | Python (FastAPI) + Isolation Forest (scikit-learn) | Unsupervised anomaly detection — no labeled fraud data required |
| Disruption Forecaster | Python + Keras LSTM | Time-series forecasting on sequential weather + AQI data |
| Database | MongoDB Atlas | Flexible schema for heterogeneous worker, policy, claim, and event documents |
| Weather / AQI Data | OpenWeatherMap API (free tier) | Current conditions + 7-day forecast + air pollution endpoint |
| Platform API | Mocked REST service (Node.js) | Simulates order dispatch feed and surge pricing data |
| Payment | Razorpay Test Mode + UPI Simulator | Demonstrates instant disbursement without live funds |
| Authentication | JWT + OTP (mocked SMS gateway) | Stateless auth aligned with mobile-first UX |

### Service Interaction Map

```
+-------------------+        REST        +---------------------+
|  Worker PWA       +<------------------>+  Core API (Go/Gin)  |
+-------------------+                    +----------+----------+
                                                    |
                              +---------------------+---------------------+
                              |                     |                     |
                    +---------v--------+  +---------v--------+  +--------v---------+
                    |  Premium Engine  |  |  Trigger Monitor |  |  Claim Processor |
                    |  (Python/XGBoost)|  |  (Go goroutines) |  |  (Go service)    |
                    +--------+---------+  +---------+--------+  +--------+---------+
                             |                      |                    |
                    +--------v---------+  +---------v--------+  +--------v---------+
                    |  MongoDB Atlas   |  |  External APIs   |  |  SPS Engine      |
                    |  (Primary store) |  |  OpenWeatherMap  |  |  (Isolation      |
                    |                  |  |  Mock Platform   |  |   Forest model)  |
                    +------------------+  +------------------+  +------------------+
```

---

## 11. Development Roadmap

### Phase 1 — Ideation and Foundation (March 4 – 20) [Complete]

- [x] Persona research and income-loss quantification
- [x] Product framing defined (income intelligence + parametric protection)
- [x] AI pricing model architecture designed
- [x] 5 parametric triggers defined and scoped
- [x] Anti-fraud and adversarial anti-spoofing architecture designed
- [x] Tech stack finalized
- [x] Repository and README established

### Phase 2 — Automation and Protection (March 21 – April 4)

- [ ] Worker registration and onboarding flow
- [ ] Sunday Night Income Forecast Card UI
- [ ] Weekly premium engine — XGBoost model with synthetic training data
- [ ] 5 parametric triggers connected to mock/live APIs
- [ ] SPS scoring pipeline (device + behavioral + network signal classes)
- [ ] Claim auto-initiation and soft hold UX flow
- [ ] Razorpay test mode + UPI simulator integration
- [ ] Policy management module

### Phase 3 — Scale and Optimise (April 5 – 17)

- [ ] Full 3-layer fraud detection with ring detection (DBSCAN clustering)
- [ ] Worker dashboard — earnings protected, active coverage, income forecast history
- [ ] Insurer admin dashboard — loss ratios, SPS distribution, syndicate alert feed
- [ ] LSTM disruption forecaster — 6-hour ahead push alerts
- [ ] Final demo build — simulated live disruption, SPS scoring, auto-payout walkthrough
- [ ] Final pitch deck (PDF)

---

## 12. Deliverables

| Deliverable | Description | Target Phase |
|---|---|---|
| Persona-optimized onboarding | Q-commerce specific registration with dark store ID, shift declaration | Phase 2 |
| AI risk profiling | XGBoost weekly premium engine with 5-variable scoring | Phase 2 |
| Dynamic weekly policy | Per-worker policy creation, Monday activation cycle | Phase 2 |
| 5 parametric triggers | Automated income-loss triggers via API integration | Phase 2 |
| Micro-payout disbursement | Mock Razorpay / UPI simulator with sub-5-minute payout | Phase 2 |
| Anti-spoofing SPS engine | 3-class signal scoring via Isolation Forest model | Phase 2 |
| Ring detection system | DBSCAN clustering on claim timestamp + device + network signals | Phase 3 |
| Worker analytics dashboard | Earnings protected, coverage status, weekly forecast | Phase 3 |
| Insurer admin dashboard | Loss ratios, SPS distribution, syndicate alert queue | Phase 3 |
| 5-minute demo video | Live disruption simulation with automated claim and payout | Phase 3 |
| Final pitch deck | Persona, AI architecture, fraud strategy, business model viability | Phase 3 |

---

## 13. Constraints & Exclusions

The following are explicitly excluded per the DEVTrails 2026 problem statement and are not present anywhere in Prahari's architecture or payout logic:

| Excluded Category | Notes |
|---|---|
| Health insurance | No medical bill coverage of any kind |
| Life insurance | No death or disability benefit |
| Accident coverage | No personal injury or hospitalization payout |
| Vehicle repair | No coverage for bike, scooter, or vehicle damage |
| Monthly or daily pricing | Strictly weekly policy cycles only |

---

**Repository:** github.com/Blazehue/prahari *(update before submission)*
**Hackathon:** Guidewire DEVTrails 2026
**Phase 1 Submission:** March 20, 2026

---

*Prahari — A sentinel for every shift.*
