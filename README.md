# Edge-Telemetry-Gateway
An industrial edge-to-cloud telemetry gateway bridging high-performance C++ edge data with a scalable Java Spring Boot backend via MQTT.

一個高效能、工業級的邊緣遙測系統。本專案展示了如何利用 **C++** 在邊緣設備進行高頻率數據採集與處理，並透過 **MQTT** 協定將資料同步至 

**Java Spring Boot** 雲端後端與 **Kubernetes (K8s)** 叢集。

## 🎯 專案目標

1. **高效能邊緣運算**：使用現代 C++ (C++17) 實作低延遲、多執行緒的邊緣數據守護行程 (Daemon)。
2. **端到端通訊整合**：實作完整的 MQTT 通訊管線，從邊緣端 (Edge) 到雲端 (Cloud) 的無縫接軌。
3. **雲原生部署**：利用 Docker 與 Kubernetes 進行雲端服務的容器化管理，展現系統的高可用性與擴展性。
4. **工業級數據處理**：在邊緣端實作數位濾波演算法，減少上傳雲端的無效數據，優化頻寬成本。

## 🏗️ 系統架構

* **Edge Node (C++)**: 部署於 Raspberry Pi 或 Linux 邊緣設備。負責模擬/讀取感測器數據、多執行緒緩衝、移動平均濾波 (MA Filter) 與資料封裝。
* **Message Broker (Mosquitto)**: 負責 Edge 與 Cloud 之間非同步訊息的傳遞。
* **Cloud Backend (Java)**: 基於 Spring Boot 3 構建，負責資料接收、持久化 (PostgreSQL/Redis) 與 RESTful API 提供。

## 🔄 系統數據流程 (System Data Flow)

本系統採用的數據流向如下，強調邊緣端的「先處理，後上傳」原則：

```mermaid
graph LR
    subgraph "Edge Node (C++ / ARM64)"
        A[Sensors/Sim] -->|Raw Data| B(MA Filter Thread)
        B -->|Refined Data| C{Logic Controller}
        C -->|JSON Package| D[MQTT Publisher]
    end

    subgraph "Infrastructure"
        D -->|Topic: telemetry/data| E[Mosquitto Broker]
    end

    subgraph "Cloud Backend (Java / K8s)"
        E -->|Subscribe| F[Spring Integration]
        F -->|Process| G[PostgreSQL / Redis]
        G -->|REST API| H[Frontend / Dashboard]
    end

---

### 第二部分：開發階段說明 (Stage 1 & Stage 2)

這部分詳細說明了如何利用你手邊的 Mac Pro M2 進行「復健」與開發。

```markdown
## 階段性實作目標 (Development Roadmap)

### 🟢 Stage 1: Local Simulation & Logic Validation (現行階段)
**目標**：在 Mac Pro M2 上完成 C++ 核心邏輯與 Java 後端的通訊驗證。
* **環境**：macOS (M2 ARM64)
* **Edge 端**：
    * 使用 `Homebrew` 安裝 `paho-mqtt-cpp`。
    * 利用 M2 的多核心優勢，實作高效能的 `std::thread` 數據產生器。
    * 開發移動平均濾波器 (Moving Average Filter) 減少模擬數據的雜訊。
* **Cloud 端**：
    * 在本機 Docker 執行 Mosquitto 與 PostgreSQL。
    * Java Spring Boot 實作基礎的 MQTT Consumer 與資料寫入邏輯。

### 🔵 Stage 2: Containerization & Distributed Testing
**目標**：將服務容器化，並在模擬的網路環境中測試穩定性。
* **環境**：Docker Desktop (Kubernetes enabled)
* **實作重點**：
    * 編寫 `Dockerfile`，將 C++ Daemon 封裝為輕量化鏡像 (Alpine Linux)。
    * 編寫 Kubernetes Manifests，練習在 K8s 中部署 Java 後端與 Database。
    * 測試在網路延遲 (Latency) 環境下，MQTT 的斷線重連機制 (QoS 1/2)。


## 🛰️ 樹莓派硬體擴展計畫 (Future Hardware Expansion)

當專案進入 Stage 3 (Real-world Edge) 時，我們將進行以下升級：

1.  **實體感測器整合**：
    * **I2C 協定通訊**：從模擬數據改為透過 C++ 直接讀取 BME280 感測器的暫存器數據。這將涉及 Linux 的 `i2c-dev` 系統呼叫。
    * **GPIO 控制**：當雲端下達指令時，樹莓派透過 C++ 控制實體 LED 燈號或繼電器。
2.  **硬體效能調優**：
    * 針對樹莓派的資源限制，優化 C++ 的記憶體配置 (避免過多的 Heap allocation)。
    * 使用 `Cross-compilation` (在 Mac M2 編譯，在 Pi 執行) 建立高效開發流。
3.  **邊緣端 K3s 部署**：
    * 將樹莓派加入 K8s 叢集，體驗真實的「雲端下發指令，邊緣自動更新」的雲原生流程。

## 🛠️ 技術棧

| 領域 | 技術 / 函式庫 |
| :--- | :--- |
| **Edge Development** | C++17, CMake, Paho MQTT C++, pthreads |
| **Cloud Backend** | Java 17/21, Spring Boot 3, Spring Integration MQTT |
| **Infrastructure** | Kubernetes (K8s), Docker, Ubuntu Server |
| **Communication** | MQTT, TCP/UDP, JSON |
| **Database** | PostgreSQL (Time-series data), Redis (Real-time cache) |

## 📂 專案目錄結構

```text
.
├── edge-daemon/            # C++ 邊緣端原始碼
│   ├── src/                # .cpp 實作
│   ├── include/            # .h 標頭檔
│   └── CMakeLists.txt      # CMake 建置設定
├── cloud-backend/          # Java Spring Boot 雲端後端
│   ├── src/                # Java 原始碼
│   └── build.gradle        # Gradle 設定
├── deployments/            # Kubernetes 與 Docker 設定檔
│   ├── k8s/                # K8s Manifests (Deployment, Service)
│   └── docker-compose.yml  # 本地開發測試環境
└── README.md
