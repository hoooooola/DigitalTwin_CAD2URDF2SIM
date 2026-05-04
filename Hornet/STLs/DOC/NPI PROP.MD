螺旋槳的**重要參數**「幾何參數」、「運行參數」以及「性能係數」三大類進行系統化整理：

### Drone SPEC
tiltRotor 3 motor vtol drone 
#### 物理特性與性能 (Physical Characteristics & Performance)
| 項目 | 英文原文 | 中文說明 |
| :--- | :--- | :--- |
| **翼展** | Wingspan: 1100mm | 機翼左端到右端的距離。 |
| **翼面積** | Wing area: 2238cm² | 機翼投影的總面積。 |
| **最大起飛重量** | MTOW: 2.5kg | 飛機能安全起飛的最大重量（含電池、載荷）。 |
| **能效/效率** | Efficiency: 2.2 Wh/km | 每飛行一公里消耗的電能，數值愈低愈省電。 |
| **巡航速度** | Cruise speed: 50-75kph | 最適合長時間穩定飛行的時速（約 14-21 m/s）。 |
| **建議槳徑** | Recommended prop: 7 inches | 建議使用的螺旋槳直徑（7 英吋）。 |

#### 空氣動力學屬性 (Aerodynamic Properties)
| 項目 | 英文原文 | 中文說明 |
| :--- | :--- | :--- |
| **翼根翼型** | Root airfoil: NACA 4410 | 機翼靠近機身處的截面形狀（具備高升力特性）。 |
| **翼尖翼型** | Tip airfoil: NACA 2410 | 機翼外側截面形狀（兼顧速度與阻力）。 |
| **翼梢翼型** | Wingtip airfoil: NACA 0010 | 機翼最末端（或翼尖小翼）使用的對稱翼型。 |
| **翼根弦長** | Root chord: 240mm | 機翼最內側的寬度。 |
| **翼尖弦長** | Tip chord: 178mm | 機翼最外側的寬度。 |
| **平均弦長** | Average Chord: 177mm | 機翼整體的平均寬度。 |
| **翼根安裝角** | Root incidence: 3° | 翼根相對於機身軸線的傾斜角度。 |
| **翼尖安裝角** | Tip incidence: 0° | 翼尖相對於機身軸線的角度（3°到0°形成了洗流/扭轉，可防失速）。 |
| **展弦比** | Aspect ratio: 5.40 | 翼展與平均弦長的比值（反映機翼的細長程度）。 |
| **最大升阻比** | Max L/D: 22 | 滑翔性能指標，數值越高代表氣動效率越精良。 |
| **上反角** | Dihedral: 0° | 機翼相對於水平面的向上傾斜角（0° 表示機翼水平）。 |
| **後緣後掠角** | TE Sweep: -7° | 機翼後緣（Trailing Edge）的掠角（負值通常指後緣向前傾斜）。 |
#### 機翼幾何與氣動數據, 推導動力需求 aka 性能分析 (Performance Analysis)

<img width="1694" height="518" alt="Image" src="https://github.com/user-attachments/assets/cc1fd131-a78e-499a-bac1-d66d6f140747" />

### 氣動特性簡介 (Aerodynamic Overview)
Hornet 的巡航性能核心設定於 **Alpha (攻角) 2° 到 6°** 之間，這是基於以下三項氣動指標的綜合優化結果：

1. **CL (升力係數, Lift Coefficient) —— 載重基礎**
    * **數據支持：** 基於 MTOW 2.5kg 與翼面積 2238cm²。
    * **邏輯：** 在 $V_{cruise} = 65 \text{ km/h}$ 時，所需的 $C_L$ 約為 **0.5 ~ 0.6**。
    * **優點：** 此區間確保飛機能在低阻力狀態下輕鬆支撐重量，並保留充足的升力餘裕來應對動態操控。
    * **計算公式 (升力公式)：**
        $$W = \frac{1}{2} \rho V^2 S C_L$$
        其中：
        * $W$ = 重力 (Weight) = $2.5 \text{ kg} \times 9.81 \text{ m/s}^2 = 24.525 \text{ N}$
        * $\rho$ = 空氣密度 (Air Density) $\approx 1.225 \text{ kg/m}^3$ (海平面標準值)
        * $V$ = 速度 (Velocity) = $65 \text{ km/h} \approx 18.06 \text{ m/s}$
        * $S$ = 翼面積 (Wing Area) = $2238 \text{ cm}^2 = 0.2238 \text{ m}^2$
    * **代入計算：**
        $$C_L = \frac{24.525}{\frac{1}{2} \times 1.225 \times (18.06)^2 \times 0.2238} \approx \frac{24.525}{44.7} \approx 0.55$$

2. **L/D & E (升阻比與能效) —— 續航極限**
    * **定義與公式：**
        * **反比關係：** 升阻比 ($L/D$) 與能效 ($E$) 成 **反比**。$L/D$ 每提升 10%，理論能耗即可降低約 10%。
        * **系統能耗公式：**
          $$E \text{ (Wh/km)} = \frac{W}{(L/D) \cdot \eta_{total} \cdot 3.6}$$
          *(其中 $\eta_{total} = \eta_{ESC} \cdot \eta_{Motor} \cdot \eta_{Prop}$，為馬達/螺旋槳/電調的總效率)*
        * **典型推估參考：**
            * 保守估計：$0.95 \times 0.75 \times 0.60 \approx \mathbf{0.43}$
            * 高效系統：$0.97 \times 0.82 \times 0.75 \approx \mathbf{0.60}$
        * **Hornet 數據反推驗證：**
            根據設定目標 $E = 2.2 \text{ Wh/km}$ 與 $L/D = 12$：
            $$2.2 = \frac{24.525}{12 \cdot \eta_{total} \cdot 3.6} \implies \mathbf{\eta_{total} \approx 0.51}$$
            *(說明 2.2 Wh/km 是一個非常專業且務實的設計指標，約 51% 的能量轉化率符合高品質動力系統特徵)*

    * **設計決定因素：**
        * **翼型 (Airfoil Shape)：** 決定斷面基礎效率。NACA 4410 提供了優秀的高升力低阻力特性。
        * **展弦比 (Aspect Ratio)：** Hornet $AR = 5.4$。增加展弦比是降低「誘導阻力」並打磨 $L/D$ 峰值的核心手段。
    * **圖表觀察：** 升阻比 ($L/D$) 與效率指標 ($E$) 在 **2° ~ 6°** 達成峰值平原（$L/D \approx 12$）。
    * **優點：** 維持在此區間巡航，方能發揮氣動優勢並達成 **2.2 Wh/km** 的高續航標竿。

    * **L/D 優化物理邏輯 (Optimization Strategy)：**
        * **核心推導公式：** 最大升阻比的理論極限可由以下公式估量：
          $$(L/D)_{max} \approx \frac{1}{2} \sqrt{\frac{\pi \cdot AR \cdot e}{C_{D,0}}}$$
          *(其中 **$e$** 為 **奧斯瓦爾德效率因子 (Oswald Factor)**，代表翼尖渦流對效率的影響，一般高品質設計取 **0.7 ~ 0.8**)*
        * **優化路徑：**
            1. **提升展弦比 (AR)：** $(L/D)_{max}$ 與 $\sqrt{AR}$ 成正比。增加翼展 (Wingspan) 或縮短平均弦長是提升效率最具決定性的手段。
            2. **降低零升力阻力 ($C_{D,0}$ )：** 透過更流線的機身設計與表面處理，減少基礎空氣阻力。
        * **設計大師公式 (Combined Master Formula)：**
            透過將升力公式與最大升阻比條件結合，可直接導出**最佳巡航速度**的物理全貌：
            $$V_{cruise, opt} = \sqrt{\frac{2 \cdot MTOW \cdot g}{\rho \cdot S \cdot \sqrt{C_{D,0} \cdot \pi \cdot AR \cdot e}}}$$
            此公式說明了 **重量、氣動設計 ($C_{D,0}, AR, e$) 與巡航速度** 之間的連鎖反應，是動力與氣動匹配的核心依據。

#### 機體運動參數：為了動力系統（螺旋槳、馬達）與機體完美匹配，同時設定以下幾個關鍵指標

### 巡航速度推估 ($V_{cruise}$ Prediction)
最佳巡航速度（最省電速度）是指達成 **最大升阻比 (Max $L/D$)** 時的速度。

*   **推估公式：**
    $$V_{cruise} = \sqrt{\frac{2W}{\rho S C_{L, opt}}}$$
    其中 $C_{L, opt}$ 為對應最大升阻比時的升力係數。

*   **針對 Hornet 的數據代入：**
    1.  **尋找 $C_{L, opt}$：** 根據氣動圖表，Max $L/D$ 出現在 $\alpha \approx 4^\circ \sim 5^\circ$ 區間，對應的 $C_{L} \approx 0.55$。
    2.  **代入運算：**
        $$V_{cruise} = \sqrt{\frac{2 \times 24.525}{1.225 \times 0.2238 \times 0.55}} \approx 18.0 \text{ m/s} \approx \mathbf{65 \text{ km/h}}$$
*   **結論：** 65 km/h 是此機體物理上的「經濟時速」，建議以此速度來優化螺旋槳的螺角設計。



### 1. 轉換速度 (Transition Speed, $V_{trans}$)

*   $V_{trans}$ = **$1.2 \times V_{stall}$**。
*   IF $V_{stall}$ = 46 km/h, THEN $V_{trans}$ = **55 km/h**。
### 2. 螺距速度 (Pitch Speed) —— **螺旋槳設計的上限**
*   **公式：** $Pitch \times RPM$。
- 必須大於$V_{cruise}$的 **1.3 ~ 1.5 倍**。建議90~100km/h
*   如果螺距速度太接近巡航速度，螺旋槳在 65 km/h 時會沒有推力（像空轉一樣）



### 3. Disk Loading —— **影響 VTOL 效率**

*   **公式：** $MTOW / \text{總盤面積}$＝ 33.56 $kg/m^2$
*   **MTOW = 2.5kg, 3 motor, 7 inch PROP**
*   **現況分析：** 你的 7 吋槳配 2.5kg 重，盤位載荷**非常高**。
*   **設定目標：** 這會導致必須選擇**高轉速、大螺距、剛性強 (3K Carbon)** 的螺旋槳。懸停時間不會太長，設計重點應放在「巡航」而非「長時間懸停」。

### 4. 推重比
TWR = 3, 配合vtol mode 三電機需求, 巡航與vtol動力系統配置需求

### 環境與氣體動力學參數


### PROP Material
- 3K Carbon Fiber Cloth

### PROP初步初步幾何設計 (Initial Sizing)：

- 利用動量理論 (Momentum Theory) 決定PROP直徑 D


1.  **直徑 ($D$, Diameter)：** 螺旋槳旋轉時圓周的直徑。直徑越大，通常排水量或排氣量越大，低速推力越好。
2.  **螺距 ($P$ 或 $p$, Pitch)：** 螺旋槳旋轉一圈理論上可以前進的距離。
    *   *註：通常螺旋槳標示如 "10x4.5"，指的就是 10英吋直徑、4.5英吋螺距。*
3.  **槳葉數 ($B$ 或 $z$, Number of Blades)：** 常見為 2 葉、3 葉或多葉。葉數越多，運轉越平穩，但單葉效率可能略降。
4.  **盤面積 (Disk Area, $A$)：** $A = \frac{\pi D^2}{4}$。
5.  **弦長 ($c$, Chord) 與 扭角 ($\beta$, Twist Angle)：** 槳葉從根部到尖端的寬度分佈與角度變化。
6.  **實度 (Solidity, $\sigma$)：** 槳葉總面積與盤面積的比值。實度越高，能吸收的功率越大。

---

### 二、 運行與運動參數 (Operational Parameters)
這些是螺旋槳在工作時的狀態：

1.  **轉速 ($n$ 或 $RPM$)：** 每秒轉數 ($n$, rps) 或每分鐘轉數 ($RPM$)。
2.  **前進速度 ($V$ 或 $V_a$, Forward Velocity)：** 飛機或船隻移動的速度。
3.  **進速比 ($J$, Advance Ratio)：** 
    *   $$J = \frac{V}{nD}$$
    *   這是最重要的無因次參數，用來定義螺旋槳當下的工作點。
4.  **尖端馬赫數 ($M_{tip}$)：** 槳尖的合成速度與音速之比。在航空中，若槳尖接近音速，效率會因激波劇降並產生巨大噪音。

---

### 三、 性能係數與效率 (Performance Coefficients)
這些是用來評價螺旋槳「出力」與「省不省力」的指標：

1.  **推力係數 ($C_T$, Thrust Coefficient)：**
    *   $$C_T = \frac{T}{\rho n^2 D^4}$$
2.  **功率係數 ($C_P$, Power Coefficient)：**
    *   $$C_P = \frac{P}{\rho n^3 D^5}$$
    *   *(註：$P$ 為輸入螺旋槳的功率)*
3.  **扭矩係數 ($C_Q$, Torque Coefficient)：**
    *   $$C_Q = \frac{Q}{\rho n^2 D^5}$$，其中 $P = 2\pi n Q$，所以 $C_P = 2\pi C_Q$。
4.  **螺旋槳效率 ($\eta$, Propeller Efficiency)：**
    *   $$\eta = \frac{\text{輸出功率}}{\text{輸入功率}} = \frac{T \cdot V}{P} = J \cdot \frac{C_T}{C_P}$$
    *   這是判斷螺旋槳在特定速度下表現的最核心數據。

---

### 四、 剖面氣動參數 (Sectional Parameters)
這是從葉片切面（翼型/葉型）的角度來看：

1.  **升力係數 ($C_L$ 或 $C_l$)：** 槳葉切面產生的垂直於相對流向的力。
2.  **阻力係數 ($C_D$ 或 $C_d$)：** 槳葉切面產生的平行於相對流向的阻力。
3.  **攻角/迎角 ($\alpha$, Angle of Attack)：** 相對氣流與槳葉弦線的夾角。$\alpha$ 會隨著 $J$ 的改變而改變。

---

### 補充：環境參數
1.  **流體密度 ($\rho$, Density)：** 空氣或水的密度。推力與密度成正比。
2.  **雷諾數 ($Re$, Reynolds Number)：** 衡量流體粘性力與慣性力的比值，影響 $C_L$ 和 $C_D$ 的精確度。

### 總結您的筆記：
您的公式 $\eta = \frac{TV}{P} = \frac{C_T}{C_P} \cdot J$ 是**螺旋槳理論的核心**。
*   當 **$J$ 太小**（靜止）：雖然推力 $T$ 大，但 $V=0$，所以效率 $\eta = 0$。
*   當 **$J$ 太大**（飛很快）：推力 $T$ 會降到 0，效率 $\eta$ 也會歸零。
*   **設計目標：** 調整螺旋槳的直徑 $D$ 與螺距 $P$，讓螺旋槳在巡航速度時的 $J$ 值正好落在 $\eta$ 曲線的最高點。