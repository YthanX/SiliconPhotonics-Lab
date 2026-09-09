# SiliconPhotonics-Lab

一套围绕硅光波导、片上 TDLAS（可调谐二极管激光吸收光谱）与 Ansys Lumerical
的渐进式学习讲义。

口头主线统一为：

    M0：FDE 与功率 EFR 方法验证
     → M2：SNS 高空气场与显式三维泄漏
     → M4：SOI 长光程与 TDLAS 主线
          ├─ M4.5：曲率与传播候选验证
          └─ M4-2025 / M4-2026：可溯源双分支

U00–U08 是教学单元编号，并非新的研究阶段。M1/M3 只作为定义、参数来源与论文
分支检查点穿插在主线中。

进入 U00–U08 前，先阅读 F00 理论前置；F00 不占用研究阶段或教学单元编号。

## 讲义目录

| 单元 | 内容 | 讲义 |
|---|---|---|
| F00 | 理论前置：本征模、模式阶数与 quasi-TE | [完整讲义](docs/lessons/F00_EIGENMODE_FOUNDATION.md) |
| U00 | 项目地图与证据等级 | [完整讲义](docs/lessons/U00_PROJECT_MAP.md) |
| U01 | M0：FDE、本征模与 Poynting EFR | [完整讲义](docs/lessons/U01_M0_FDE.md) · [MODE实操截图与结果](docs/results/U01_MODE_FDE/README.md) |
| U02 | M2：SNS 高空气场与三维泄漏 | [待整理](docs/lessons/U02_M2_SNS.md) |
| U03 | M4：SOI strip/rib 截面与模式追踪 | [学习记录版](docs/lessons/U03_M4_CROSS_SECTIONS.md) |
| U04 | M4：真实路径与长度加权 Gamma | [学习记录版](docs/lessons/U04_M4_PATH_GAMMA.md) |
| U05 | M4.5：曲率、EME 与失效 FDTD | [学习记录版](docs/lessons/U05_M45_CURVATURE_PROPAGATION.md) |
| U06 | M4-2025 / M4-2026 分支审计 | [学习记录版](docs/lessons/U06_M4_BRANCH_AUDIT.md) |
| U07 | Beer–Lambert、ADC、DAS 与 WMS/2f | [学习记录版](docs/lessons/U07_TDLAS_SIGNAL_CHAIN.md) |
| U08 | 综合验收与三分钟项目陈述 | [学习记录版](docs/lessons/U08_FINAL_ACCEPTANCE.md) |

## 学习方法

每章使用同一闭环：

    最少必要理论 → GUI 最小重建 → 脚本扩展
    → 证据审计 → 主动复述 → 验收

当前发布 U00、U01 完整讲义，以及 U03–U08 的学习记录版。U02 仍保留课程路线位置，
U03–U08 的记录不代表完整三维传播、真实硬件实验或综合验收已经完成。建议一次只学
一个单元，先完成亲手任务，再回答验收题。

## 结果与证据边界

讲义主动区分五类来源：论文公开、GDS 实测、私下补充、合理假设、未验证项。

本仓库不能作为以下声明的依据：

- 已获得论文作者原始工程或原始 2026 GDS；
- 已完成可直接流片的版图；
- 已完成整条约 10 cm 器件的三维电磁传播；
- 已完成真实甲烷实验、真实 ADC 或论文 LOD 复现。

历史 M4.6 仅作为“2025 GDS 平面几何与 2026 rib 截面参数曾被混合”的反例。
现行表述使用 M4-2025 与 M4-2026。

## 软件与术语

- FDE：Finite-Difference Eigenmode，有限差分本征模；
- FDTD：Finite-Difference Time-Domain，有限差分时域；
- EME：Eigenmode Expansion，本征模展开；
- SOI：Silicon on Insulator，绝缘体上硅；
- SNS：Suspended Nanomembrane Silicon，悬浮纳米膜硅；
- EFR / Gamma：气体区域的场或功率重叠指标，必须说明积分定义；
- DAS / WMS：直接吸收光谱 / 波长调制光谱。

## 数据说明

讲义中保留用于学习的代表性数值和原工程证据文件名，但大型 Lumerical 工程、
正式机器结果、私有资料与实验数据不在本仓库中。精确复算应重新核对材料、几何、
边界、网格、模式追踪、积分口径与参数来源。
