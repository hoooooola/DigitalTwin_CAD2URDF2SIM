# CAD 模型轉換至 Isaac 指南 - ONSHAPE

在 Onshape 雲端協作環境下，將 CAD 模型轉換為 NVIDIA Isaac Sim 可用的格式（USD 或 URDF）是建立高保真數位孿生的核心路徑。Onshape 的 API 優勢讓 **自動化工具鏈** 與 **模型同步** 變得比傳統桌面 CAD 更具效率。

以下是針對 **Onshape 專屬工作流** 整理的關鍵參考與研究方向：

---

## 💡 Onshape 核心轉換路徑與工具

### 1. 基於 `onshape-to-robot` 的 URDF 自動化途徑
這是開源社群最成熟的路徑，特別適合需要 **URDF** 格式的強化學習（Isaac Lab / Orbit）環境。

*   **關鍵工具**： [onshape-to-robot](https://github.com/ykhedar/onshape-to-robot.git)
*   **技術特點**：
    *   **自動計算物理屬性**：透過 Onshape API 自動獲取零件的質量、重心（CoM）與慣性矩（Inertia）。
    *   **約束轉換**：將 Onshape 的 `Mate Connectors` 與 `Mates` 自動轉化為 URDF 中的 `Joints`。
*   **研究價值**： 解決了手動編輯 URDF 的痛點，透過 `config.json` 即可快速叠代機器人結構。
*   **推薦文獻**： *Describing Robots from Design to Learning* (提及了類似 ACDC4Robot 的自動化邏輯)。

### 2. 基於 `Omniverse Onshape Connector` 的原生 USD 路徑
這是 NVIDIA 官方推薦的 **高保真 (High-Fidelity)** 路徑，直接與 Isaac Sim 生態系接軌。

*   **關鍵工具**： `NVIDIA Omniverse Onshape Connector`
*   **核心優勢**：
    *   **實時同步 (Live Sync)**：在 Onshape 中修改幾何外觀或組裝，Isaac Sim 中的 USD 模型可同步更新。
    *   **材質保留**：保留複雜的渲染材質與視覺細節。
    *   **層級化 USD**：生成的 USD 結構保持了 Onshape Assembly 的層級關係，方便在 Omniverse 中進行場景搭建。
*   **關鍵字搜尋**： `Onshape to Omniverse USD workflow` 或 `Digital Twin CAD synchronization`.

### 3. 物理參數校準與 Onshape Metadata
利用 Onshape 的 **自定義屬性 (Custom Properties)** 來優化 Isaac Sim 中的物理表現。

*   **實戰建議**：
    *   **質量屬性覆蓋**：在 Onshape 中預先設定好材料密度，確保導出時的物理屬性具備物理真實性。
    *   **碰撞體優化**：在 Onshape 中建立一個簡化版的 `Collision Assembly`，專門用於導入後的碰撞計算，以提升仿真效能（大型場景優化）。
*   **參考專案**： `Orbit-Surgical` 專案中針對不同醫療器械從 Onshape 導入後的物理校準流程。

---

## 🚀 Onshape 專用工作流對比

| 轉換需求 | 推薦工具 | 優點 | 缺點 | 檔案路徑 |
| :--- | :--- | :--- | :--- | :--- |
| **經典機器人開發** | `onshape-to-robot` | 符合 ROS/ROS 2 生態、自動生成簡化碰撞網格、跨模擬器（PyBullet, MuJoCo） | 視覺細節較差、模型修改後需重新執行指令導出 | CAD → URDF |
| **高階數位孿生** | `Onshape Connector (openUSD)` | Live Link 即時同步、支援物理材質（PBR）、自動處理關節約束 | 需安裝 Omniverse 環境、硬體（GPU）要求較高 | CAD → OpenUSD |
| **場景／佈局規劃** | `USD Layout Export` | 適合大型靜態場景、架構層級清晰（Layers）、有利於多人協作 | 缺乏運動學連桿（Joints），機器人不能動，僅限靜態 | CAD → USD |

---
> [!IMPORTANT]
> **Onshape 實作建議**：
> 1. **Mate Connectors** 是關鍵：確保在 Onshape 中定義好名稱明確的 Mate Connectors，這將大幅簡化導入 Isaac Sim 後的 Link 與 Joint 辨識。
> 2. **版本控制**：利用 Onshape 的 `Versions and History` 功能，為每次導出的模型記錄對應的 CAD 版本，確保數位孿生具備可追溯性。