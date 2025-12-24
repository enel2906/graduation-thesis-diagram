# Stock Pattern Detection System - Use Case Diagrams

PlantUML use case diagrams for the stock pattern detection system.

## 📋 Use Case Diagrams List

### 1. General Use Case
**File:** [01-general-use-case.puml](01-general-use-case.puml)

**Description:** System overview including:
- Main actors: Investor, Administrator, System
- External systems: VnStock API, MongoDB, RabbitMQ
- Core use case packages:
  - Authentication
  - Market Analysis
  - Portfolio Management
  - Real-time Updates
  - Admin Functions
  - Data Pipeline

**Relationships:**
- Include: Dependent use cases
- Extend: Optional features
- Uses: External system interactions

---

### 2. Authentication & Authorization
**File:** [02-authentication-use-case.puml](02-authentication-use-case.puml)

**Description:** Authentication and authorization details:
- **Registration:**
  - Data validation and duplicate check
  - Password hashing with BCrypt
  - User creation and JWT token generation

- **Login:**
  - Credential verification
  - Access Token (15 min) + Refresh Token (7 days)
  - Session management

- **OAuth:**
  - Google OAuth 2.0 flow
  - Authorization code exchange
  - User info retrieval and mapping

- **Token Management:**
  - Auto token refresh
  - Validation and renewal

- **Authorization:**
  - Access control
  - Role verification (USER/ADMIN)

**Technology:**
- JWT with HS256
- BCrypt hashing
- Spring Security + OAuth2

---

### 3. Stock Data Management
**File:** [03-stock-data-management-use-case.puml](03-stock-data-management-use-case.puml)

**Description:** Stock data collection and management:

- **Data Collection:**
  - Fetch VN stocks via VnStock API
  - Fetch US stocks via YFinance
  - Parse OHLC data and validate

- **Storage:**
  - Composite ID: `{symbol}_{dateStr}`
  - Bulk upsert to prevent duplicates
  - Index management

- **Periodic Update:**
  - Background task every 15 minutes
  - Incremental sync from last date
  - Loop through all stocks

- **Publishing:**
  - Format JSON messages
  - Publish to RabbitMQ topic
  - Routing: `stock.update.{symbol}`

- **APIs:**
  - GET /api/stocks
  - GET /api/candlesticks/{symbol}
  - POST /api/admin/stocks
  - GET /api/company/news/{symbol}
  - GET /api/company/financial/{symbol}

**Data Sources:**
- VnStock API (VN stocks)
- YFinance (US stocks)
- MongoDB (storage)
- RabbitMQ (real-time)

---

### 4. Pattern Detection
**File:** [04-pattern-detection-use-case.puml](04-pattern-detection-use-case.puml)

**Description:** Candlestick and chart pattern detection:

- **Candlestick Patterns (49 total):**
  - Single Candle (11): Hammer, Doji, Marubozu, etc.
  - Two Candle (15): Engulfing, Harami, Piercing Line, etc.
  - Three Candle (18): Morning/Evening Star, Three Soldiers, etc.
  - Multi Candle (5): Three Methods, Line Strike, etc.

- **Chart Patterns (12 total):**
  - Triangles (3): Ascending, Descending, Symmetrical
  - Advanced: Cup & Handle, Double Tops/Bottoms, H&S, Flag, Pennant

- **Detection Process:**
  - Load OHLC data
  - Calculate metrics
  - Identify patterns
  - Score confidence

- **Multi-Selection:**
  - Search and browse patterns
  - Select multiple simultaneously
  - Real-time application

- **Visualization:**
  - Markers (▲ bullish, ▼ bearish)
  - Trend lines and zones
  - Tooltips with info
  - Color coding

**Algorithm:**
- Body/shadow ratio analysis
- Trendline convergence
- Breakout detection

---

### 5. Real-Time Monitoring
**File:** [05-realtime-monitoring-use-case.puml](05-realtime-monitoring-use-case.puml)

**Description:** Real-time data pipeline via RabbitMQ and WebSocket:

- **Publishing (Python → RabbitMQ):**
  - Fetch new OHLC data
  - Transform to JSON
  - Publish to topic exchange
  - Routing: `stock.update.{symbol}`

- **Message Broker:**
  - Route by pattern matching
  - Queue: `stock.live.updates`
  - Persist messages

- **Consumption (RabbitMQ → Java):**
  - Listen on queue
  - Deserialize JSON
  - Validate and acknowledge

- **WebSocket Broadcasting:**
  - Broadcast to subscribers
  - Topic: `/topic/stock-updates/{symbol}`
  - Handle failures

- **WebSocket Connection (React):**
  - SockJS + STOMP client
  - Subscribe to topics
  - Auto reconnect

- **Chart Update:**
  - Parse data
  - Update chart series
  - Recalculate indicators
  - Re-detect patterns

- **Connection Management:**
  - Heartbeat (4s interval)
  - Retry logic
  - Fallback to HTTP polling

**Protocol Stack:**
- Python: aio-pika
- RabbitMQ: Topic Exchange
- Java: Spring AMQP + WebSocket
- React: SockJS + STOMP

---

### 6. Admin Management & Additional Features Use Case
**File:** [06-admin-additional-features-use-case.puml](06-admin-additional-features-use-case.puml)

**Mô tả:** Quản trị hệ thống và tính năng bổ sung:

- **Stock Management:**
  - List, add, delete stocks
  - Fetch 10-year history
  - Validation and duplicate check

- **System Statistics:**
  - Count by market
  - Health monitoring
  - Sync status

- **Watchlist:**
  - Create/manage lists
  - Add/remove stocks
  - Organize portfolio

- **Price Board:**
  - Real-time updates
  - Price change indicators
  - Quick chart access

- **News & Reports:**
  - Company news (VCI source)
  - Financial reports:
    - Balance Sheet
    - Income Statement
    - Cash Flow
    - Financial Ratios
  - Period: Year/Quarter

- **Chart Features:**
  - Zoom/pan controls
  - Dark/light theme
  - Export functionality

---

### 7. Stock Chart Interaction Use Case
**File:** [07-stock-chart-interaction-use-case.puml](07-stock-chart-interaction-use-case.puml)

**Mô tả:** Tương tác người dùng với biểu đồ:

- **Stock Selection & Loading (UC-07.1 đến UC-07.7):**
  - Search modal với filter
  - Load OHLC và cache trong ref

- **Pattern Selection (UC-08.1 đến UC-08.8):**
  - Indicators Modal
  - Browse 49 patterns theo categories
  - Multi-select với checkbox
  - Search by name
  - Apply và detect

**File:** [07-stock-chart-interaction-use-case.puml](07-stock-chart-interaction-use-case.puml)

**Description:** User interaction with stock charts
  - Tooltips on hover
:**
  - Search with filters
  - Load OHLC data
  - Initialize chart

- **Pattern Selection:**
  - Browse 49 patterns by category
  - Multi-select capability
  - Search functionality
  - Apply and detect

- **Visualization:**
  - TradingView Lightweight Charts
  - Pattern markers and lines
  - Interactive tooltips
  - Color coding

- **Navigation:**
  - Zoom and pan controls
  - Auto-scale
  - Reset view

- **Time Range:**
  - Presets: 1M, 3M, 6M, 1Y, ALL
  - Custom date range

- **Theme:**
  - Dark/light mode
  - Color customization

**Chart Library:**
- TradingView Lightweight COverview
**File:** [08-system-architecture-context.puml](08-system-architecture-context.puml)

**Description:** Overall system architecture:

**Components:**
- **Frontend:**
  - React Client (Port 5173)
  - WebSocket Client (SockJS)

- **Backend:**
  - Java Spring Boot (Port 60)
  - Python FastAPI (Port 8000)

- **Services:**
  - Authentication
  - Pattern Detection
  - Stock Data Management
  - Real-time Updates

**External Systems:**
- MongoDB: Data storage
- RabbitMQ: Message broker
- VnStock API: Market data
- Google OAuth: Authentication

**Technology Stack:**
- Frontend: React 19 + Vite
- Backend: Spring Boot 3.3.5, FastAPI
- Database: MongoDB
- Message Queue: RabbitMQ
- Real-time: WebSocket

### Render diagram local:
```bash
# Install PlantUML
npm install -g node-plantuml

# Generate PNG
puml generate 01-general-use-case.puml -o output/

# Generate SVG
puml gUsage

### View Online:
1. Open [PlantUML Online Editor](http://www.plantuml.com/plantuml/uml/)
2. Copy .puml file content
3. Paste to view diagram

### Local Rendering:
```bash
# Install PlantUML
npm install -g node-plantuml

# Generate PNG
puml generate 01-general-use-case.puml -o output/

# Generate SVG
puml generate *.puml -o output/ -f svg
```

### VS Code:
1. Install extension: "PlantUML" by jebbs
2. Open .puml file
3. Press `Alt+D` for preview

---

## 📊 Summary

- **Total diagrams:** 8
- **Use cases:** 60+ (simplified from 100+)
- **Actors:** 
  - Primary: User, Administrator
  - Secondary: System, External APIs
- **External systems:** VnStock API, Google OAuth, MongoDB, RabbitMQ
- **Services:** 15+ components

---

## 📚 References

- [PlantUML Use Case Diagram](https://plantuml.com/use-case-diagram)
- [Stock Pattern Detection System](../../stock-pattern-detect-system/)
- [VnStock Guide](../../vnstock-agent-guide-main/docs/)
- [TradingView Charts](https://tradingview.github.io/lightweight-charts/)

---

## ✏️ Notes

- All diagrams use PlantUML syntax
- Color-coded actors for clarity
- Notes provide technical details
- Relationships: include, extend, uses
- Simplified and concise version

---

**Project:** Graduation Thesis  
**Date:** December 2025  
**Version:** Concise English Edition