# TARE Planner Garage 環境使用說明

## 系統需求

- Ubuntu 22.04 with ROS2 Humble 或 Ubuntu 24.04 with ROS2 Jazzy
- Gazebo 模擬器
- Git

## 專案結構

```
ros2_tare_/
├── autonomous_exploration_development_environment/  # 開發環境與模擬器
└── tare_planner/                                   # TARE 探索規劃器
```

## 安裝步驟

### 1. 下載專案

```bash
cd ~
mkdir -p ros2_tare_
cd ros2_tare_

# 下載開發環境
git clone https://github.com/cmu-exploration/autonomous_exploration_development_environment.git
cd autonomous_exploration_development_environment
git checkout humble  # 或 jazzy，根據您的 ROS2 版本

# 下載 TARE Planner
cd ~/ros2_tare_
git clone https://github.com/caochao39/tare_planner.git
cd tare_planner
git checkout humble-jazzy
```

### 2. 下載環境檔案

```bash
cd ~/ros2_tare_/autonomous_exploration_development_environment/src/vehicle_simulator/mesh
bash download_environments.sh
```

這會下載所有模擬環境，包括：
- campus
- forest
- garage
- indoor
- tunnel

### 3. 編譯專案

#### 編譯開發環境

```bash
cd ~/ros2_tare_/autonomous_exploration_development_environment
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
```

#### 編譯 TARE Planner

```bash
cd ~/ros2_tare_/tare_planner
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
```

## 運行 Garage 環境探索

### 第一個終端：啟動 Garage 模擬環境

```bash
cd ~/ros2_tare_/autonomous_exploration_development_environment
source install/setup.bash
export GAZEBO_MODEL_PATH=$PWD/src/vehicle_simulator/mesh:$GAZEBO_MODEL_PATH
ros2 launch vehicle_simulator system_garage.launch
```

這會啟動：
- Gazebo 模擬器與 garage 環境
- Vehicle Simulator（車輛模擬器）
- LiDAR 感測器
- 地形分析節點
- Local Planner（局部規劃器）
- RViz 視覺化工具

### 第二個終端：啟動 TARE Planner

```bash
cd ~/ros2_tare_/tare_planner
source install/setup.bash
ros2 launch tare_planner explore_garage.launch
```

這會啟動：
- TARE Planner 探索節點
- 第二個 RViz 視覺化窗口（顯示探索路徑）

### 確認運行狀態

當系統成功啟動時，您會在 TARE Planner 終端看到：

```
Exploration Started
```

## 視覺化說明

### RViz 窗口 1（來自 system_garage）
顯示：
- 3D 點雲數據（LiDAR 掃描）
- 環境地圖
- 機器人位置
- 地形分析結果

### RViz 窗口 2（來自 tare_planner）
顯示：
- 探索路徑規劃
- 前沿點（Frontier points）
- 全域與局部規劃資訊
- TSP 路徑優化結果

## 切換到其他環境

要在不同環境中運行，只需替換 `garage` 為其他環境名稱：

可用環境：
- `campus` - 校園環境
- `indoor` - 室內環境
- `garage` - 車庫環境
- `tunnel` - 隧道環境
- `forest` - 森林環境

### 範例：運行 Indoor 環境

終端 1：
```bash
ros2 launch vehicle_simulator system_indoor.launch
```

終端 2：
```bash
ros2 launch tare_planner explore_indoor.launch
```

## 常見問題

### Q: Gazebo 顯示空白或黑屏
A: 這是正常的。本系統主要使用 RViz 進行視覺化，而不是 Gazebo GUI。請查看 RViz 窗口。

### Q: 看不到環境模型
A: 確認已經：
1. 執行了 `download_environments.sh` 下載環境
2. 設置了 `GAZEBO_MODEL_PATH` 環境變數
3. 所有實體成功生成（檢查終端輸出）

### Q: RViz 視窗是黑色的
A: 等待幾秒鐘，讓 LiDAR 開始掃描環境。點雲數據會逐漸出現。

### Q: 機器人不移動
A: 確認：
1. TARE Planner 顯示 "Exploration Started"
2. 兩個 launch 文件都在運行
3. 檢查是否有錯誤訊息

## 停止系統

在每個終端按 `Ctrl+C` 停止運行。

如果需要強制終止所有進程：
```bash
killall -9 gzserver gzclient rviz2
```

## 技術細節

### TARE Planner 特點
- 分層框架：局部密集處理 + 全域稀疏處理
- TSP 路徑優化
- 實時探索路徑規劃
- 自適應前沿檢測

### 系統架構
1. **Gazebo** - 物理模擬
2. **Vehicle Simulator** - 車輛動態模擬
3. **Local Planner** - 避障與局部導航
4. **Terrain Analysis** - 地形可通過性分析
5. **TARE Planner** - 探索路徑規劃

## 參考資料

- [TARE Planner 官方網站](https://www.cmu-exploration.com/tare-planner)
- [CMU Exploration 項目](https://www.cmu-exploration.com)
- [論文] C. Cao et al., "Representation Granularity Enables Time-Efficient Autonomous Exploration in Large, Complex Worlds", Science Robotics, 2023

## 授權

請參閱各專案的 LICENSE 文件。
