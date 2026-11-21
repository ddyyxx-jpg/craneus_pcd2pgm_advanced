# craneus_pcd2pgm_advanced

> 面向 RoboMaster / RMUC 场地的**高级点云建图方法集**。
> 每个方法一个独立子目录，含完整脚本、参数、文档与可验证的产物；
> 数据（点云 / 官方模型 / 赛事资料）不随仓库分发。

## 方法一览

| 目录 | 方法 | 一句话说明 |
|---|---|---|
| [`fusion_mapping/`](fusion_mapping/README.md) | **多源点云融合 + 官方模型形状参照** | 两拨人各自扫的场馆点云 → 配准/融合 → 以官方模型做形状、扫描做尺寸 → 干净规整的场地级点云 |
| [`clearance_ground_mapping/`](clearance_ground_mapping/README.md) | **净空法多级地面归一化 + 栅格建图** | 逐格估计局部地面、把多级地面压平到同一基准，再复用固定阈值 `pcd2pgm` 建图，后处理成单连通、隧道全通的地图 |

## 快速开始

### 多源点云融合（无需真实数据，合成数据即可验证）

```bash
cd fusion_mapping
pip install -r requirements.txt
./tests/run_smoke_test.sh          # 合成场馆 → 全流程 6 步 → 断言 PASSED
# 真实数据：改 run_pipeline.sh 顶部输入路径后
./run_pipeline.sh
```

### 净空法地面归一化 + 建图

```bash
cd clearance_ground_mapping
pip install -r requirements.txt
python3 scripts/clearance_ground_norm.py --pcd data/rmuc_2026.pcd --out maps/norm.pcd
# 再用 ROS 2 pcd2pgm 节点建图 + scripts/build_final_map.py 后处理
# 详见 clearance_ground_mapping/README.md
```

## 两个方法的关系

- **fusion_mapping** 解决"**数据从哪来**"：多源扫描的坐标统一、动态剔除、以官方模型为形状参照的规整化；
- **clearance_ground_mapping** 解决"**点云怎么变成地图**"：多级地面归一化 + 固定阈值建图 + 图级后处理。

两者可以串联：先 fusion 得到干净场地级点云，再喂给净空法建图流程。

## 目录约定

```
<method>/
├── README.md          # 方法说明 / 效果 / 复现步骤
├── requirements.txt
├── scripts/           # 可执行脚本（argparse 风格，无硬编码私有路径）
├── configs/ 或 config/# 参数
├── docs/              # 算法细节 / 框架图
├── maps/ previews/    # 产物（栅格地图 / 预览图，如适用）
└── tests/             # 冒烟测试（合成数据，可离线运行）
```

## 依赖

各子目录的 `requirements.txt` 独立；RPM（净空法建图）另需 ROS 2 + 开源 `pcd2pgm` 节点。

## License

MIT，见 [LICENSE](LICENSE)。
