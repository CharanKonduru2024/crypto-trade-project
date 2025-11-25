# 🚀 Cryptocurrency Real-Time Trading & Prediction System  
A full real-time cryptocurrency analytics and algorithmic trading simulation pipeline built using:

- **Docker & Docker Compose**
- **Python**
- **Redpanda (Kafka-like message broker)**
- **Streamlit**
- **DuckDB**
- **Machine Learning (Random Forest-style classification)**
- **Cron-based automation**
- **Kraken Trades API (real trade data)**

This system runs continuously and performs:

1. **Real-time BTC-USD trade ingestion**
2. **Live 1-minute candlestick creation**
3. **Feature engineering + ML predictions**
4. **Hourly model training**
5. **Automated trading strategy simulation**
6. **Full real-time dashboard visualization**
7. **Backfilling historical data**
8. **Gap fixing when services restart**

A complete end-to-end production-style data engineering + machine learning project.

---

# 🧠 What This Project Demonstrates

✔ Real-time streaming pipelines  
✔ Kafka-style messaging (Redpanda)  
✔ Event-driven microservices  
✔ Docker container orchestration  
✔ ML model training + serving  
✔ DuckDB analytics engine  
✔ Cronjob-based automation  
✔ Real-time dashboards  
✔ System architecture design  

Perfect for **Data Engineering**, **ML Engineering**, **MLOps**, and **Quant/Trading** roles.

---

# 📡 System Architecture

```
Kraken API (live trades)
           │
           ▼
Realtime Collector ───▶ Redpanda Topic: trades
           │
           ▼
     Candle Maker (1m OHLCV)
           │
           ▼
       DuckDB Storage
           │
   ┌───────┴────────────────────────┐
   ▼                                 ▼
Prediction Service (ML model)   Streamlit Dashboard
           │
           ▼
       TradeBot Simulation
```

---

# 🧩 Component Overview

### **1️⃣ fetch_historical_trade_data.py**
Downloads past trade data (30 days, configurable).  
Used to pre-populate the system so candles + training can begin immediately.

---

### **2️⃣ fetch_realtime_data.py**
Continuously polls Kraken API and pushes live trades to Redpanda.

- Trade price  
- Volume  
- Timestamp  
- Buy/Sell  
- Market/Limit  
- Trade ID  

Published to topic: **trades**

---

### **3️⃣ candle_maker.py**
Consumes live trades → groups them into **1-minute candles** (OHLCV):

- **O**pen  
- **H**igh  
- **L**ow  
- **C**lose  
- **V**olume  
- **Trade Count**

Stored in DuckDB table **candles**.

---

### **4️⃣ process_candle.py (Prediction Service)**
Every minute:

- Fetches latest candle  
- Converts to ML features  
- Loads latest trained model  
- Predicts **UP or DOWN** for the next minute  
- Saves prediction to DuckDB  

Also used by the trading bot.

---

### **5️⃣ trade_bot.py**
Simulates **three trading strategies** using predictions:

| Strategy | Description |
|----------|-------------|
| ⚡ Dynamic | Aggressive entry/exit behavior |
| ⚖️ Balanced | Moderate trading frequency |
| 🔥 Ultra Aggressive | Very high-risk high-frequency |

Tracks:

- Portfolio value  
- Units held  
- Trade history  
- Strategy performance over time  

---

### **6️⃣ train.py**
Hourly ML model retraining using fresh candle + prediction data.

Outputs model metrics:

- **F1 Score**  
- **Accuracy**  
- **Precision**  
- **Recall**

Models are saved with timestamp, e.g.:

```
models/20250507_130136.pkl
```

---

### **7️⃣ Streamlit Dashboard**

📈 Real-time candlestick chart (last 6–8 hours)  
🤖 Latest prediction (UP or DOWN)  
📊 Model performance summary  
💰 Live strategy balances  
📉 Strategy value history  

Accessible on port **8501**.

---

# 🖥️ Screenshot Examples
(Add your dashboard images here)

```
![Real-time Dashboard](images/dashboard.png)
```

---

# ⚙️ Technologies Used

| Area | Tools |
|------|-------|
| Streaming | Kraken API, Redpanda |
| Storage | DuckDB |
| ML | scikit-learn |
| Containers | Docker, Docker Compose |
| Dashboard | Streamlit |
| Automation | Cronjobs |
| Logging | Container logs |

---

# 🏗️ How to Run the Project (Complete Steps)

## **Step 1: Start Docker Containers**

```bash
docker build -t ml-trading-service ./services/base
docker-compose up -d
```

---

## **Step 2: Fetch Historical Data (30 days)**

```bash
docker exec -it backfill bash
time python fetch_historical_trade_data.py
time python load_historical_data_as_candles.py
```

---

## **Step 3: Start Realtime Trade Collection**

```bash
docker exec -it realtime bash
tmux
python fetch_realtime_data.py
```

Detach tmux:  
Press `CTRL + b` then `d`

---

## **Step 4: Start Candle Maker Service**

```bash
docker exec -it candle_maker bash
tmux
python candle_maker.py
```

---

## **Step 5: Start Prediction Service**

```bash
docker exec -it predict bash
tmux
python process_candle.py
```

---

## **Step 6: Get “Gap” Data After Restart**

```bash
docker exec -it backfill bash
time python fetch_historical_trade_data.py   # updated since timestamp
time python load_historical_data_as_candles.py
```

---

## **Step 7: Start TradeBot**

```bash
docker exec -it trade_bot bash
time python trade_bot.py
```

### Cronjob (every 5 minutes):

```
*/5 * * * * sleep 30; /usr/local/bin/python /app/trade_bot.py >> /var/log/container.log 2>&1
```

---

## **Step 8: Model Training**

```bash
docker exec -it train bash
time python train.py
```

### Cronjob (hourly):

```
30 * * * * sleep 30; /usr/local/bin/python /app/train.py > /var/log/container.log 2>&1
```

---

## **Step 9: Streamlit Dashboard**

```bash
docker exec -it streamlit_app bash
tmux
streamlit run streamlit_app.py
```

View at:

```
http://<YOUR-DROPLET-IP>:8501
```

---

# 🧰 TMUX Commands

| Action | Command |
|--------|----------|
| List sessions | `tmux ls` |
| Attach | `tmux attach -t <name>` |
| Detach | `CTRL + b`, then `d` |

---

# 📝 VIM Quick Guide

| Action | Key |
|--------|-----|
| Insert mode | `i` |
| Save | `:w` |
| Save & exit | `:wq` |
| Exit w/o save | `:q!` |

---

# 📜 License
This project is for educational purposes. Not financial advice.


# Cryptocurrency TradeBots

## Steps 1: Start up the docker containers
1. Execute `docker build -t ml-trading-service ./services/base`
2. Execute `docker-compose up -d`

## Step 2: Fetch historical data from the past 30 days
1. Execute `docker exec -it backfill bash`
2. Execute `time python fetch_historical_trade_data.py`
3. Execute `time python load_historical_data_as_candles.py`

## Step 3: Start fetching the realtime crypto trades
1. Execute `docker exec -it realtime bash`
2. Execute `tmux`
3. Execute `python fetch_realtime_data.py`

## Step 4: Start candle-maker service
1. Execute `docker exec -it candle_maker bash`
2. Execute `tmux`
3. Execute `python candle_maker.py`


## Step 5: Start prediction service
1. Execute `docker exec -it predict bash`
2. Execute `tmux`
2. Execute `python process_candle.py`

## Step 6: Get gap data
1. Execute `docker exec -it backfill bash`
2. Execute `time python fetch_historical_trade_date.py` after update since last time stamp
3. Execute `time python load_historical_data_as_candles.py`

## Step 7: Setup Tradebot
1. Execute `docker exec -it trade_bot bash`
2. Execute `time python trade_bot.py`
3. Setup cronjob:
   1. crontab -e (quickly learn vim to add a line!)
   2. `*/5 * * * * sleep 30; /usr/local/bin/python /app/trade_bot.py >> /var/log/container.log 2>&1`

## Step 8: Setup Model training
1. Execute `docker exec -it train bash`
2. Execute `time python train.py`
3. Setup cronjob:
   1. crontab -e (quickly learn vim to add a line!)
   2. `30 * * * * sleep 30; /usr/local/bin/python /app/train.py > /var/log/container.log 2>&1`


## Step 9: Setup streamlit app
1. Execute `docker exec -it streamlit_app bash`
2. Execute `tmux`
2. Execute `streamlit run streamlit_app.py`


## TMUX

- **List sessions:**
  `tmux ls`

- **Attach to session:**
  `tmux attach -t <session-name>`

- **Detach:**
  Press `Ctrl+b`, then `d`


## VIM

- **Enter edit mode:**
  Press `i`

- **Paste (in insert mode):**
  Use your terminal's paste (e.g., `Ctrl+Shift+V` or right-click paste)

- **Save and exit:**
  Press `Esc`, then type `:wq` and press `Enter`

