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
