# U01：M0 FDE、本征模与 Poynting EFR

配套实操结果：[MODE/FDE界面、四模表与模场判读](../results/U01_MODE_FDE/README.md)。

## 核心问题

给定波长、材料和截面，怎样追踪同一条 quasi-TE0 分支，并计算气体区纵向功率比例？

## 最少必要理论

均匀直波导沿 z 传播时：

$$
\mathbf E_m(x,y,z)=\mathbf e_m(x,y)e^{i\beta_m z},
\qquad \beta_m=k_0n_{\mathrm{eff},m}.
$$

FDE 只需求垂直传播方向的 x-y 截面。波导变长会累计相位和损耗，不会改变均匀
截面的本征场形。

纵向 Poynting 分量为：

$$
S_z=\frac{1}{2}\,\Re\left[(\mathbf{E}\times\mathbf{H}^{*})\cdot\hat{\mathbf{z}}\right].
$$

M0 的功率型 EFR 为：

$$
\mathrm{EFR}_P=
\frac{\iint_{\mathrm{gas}}S_z\,dA}
{\iint_{\mathrm{all}}S_z\,dA}.
$$

它不是 M2/M4 采用的空气区 $|E|^2$ Gamma。

## 输出指标

| 指标 | 回答什么 | 不能替代 |
|---|---|---|
| neff | 传播常数、相位和束缚程度 | 透射率 |
| TE fraction | 模式偏振更像 TE 还是 TM | 基模阶数和未跳模证明 |
| 模场 | 场在核心、基底和气体区的形状 | 输入耦合系数 |
| Poynting EFR | 气体区纵向功率占比 | 空气场强 Gamma |

## 代表性机器基准

| 参数 | 值 |
|---|---:|
| 波长 | 4.23 µm |
| Si 核心宽/高 | 1.0/0.6 µm |
| Si / sapphire / gas 折射率 | 3.42 / 1.67 / 1.0 |
| trial modes | 4 |
| neff | 1.948420 |
| TE fraction | 0.939151 |
| Poynting EFR | 0.1023005 |

原机器证据文件名：2014_sos_waveguides/outputs/strip_te_baseline_metrics.json。
论文约为 0.10；当前结果与其绝对差约 0.00230。

## 模式追踪

每个宽度点都会重新求解和编号。“始终选 mode1”不是物理依据。至少联合检查：

1. neff 是否连续；
2. TE fraction 是否保持 quasi-TE；
3. 核心场参与是否合理；
4. 模场形状是否连续；
5. 必要时计算相邻模场重叠。

已完成的strip宽度盲测说明了“连续”与“线性”不是一回事：

| W (µm) | neff | TE fraction | Poynting EFR |
|---:|---:|---:|---:|
| 0.9 | 1.76837 | 92.84% | 13.0774% |
| 1.0 | 1.94842 | 93.92% | 10.2300% |
| 1.05 | 2.02554 | 95% | 9.42197% |
| 1.1 | 2.09472 | 95.33% | 8.91272% |

这些点的neff、偏振、单峰模场和EFR趋势共同支持同一条quasi-TE0分支。模式参数随
几何平滑变化通常连续，但不保证线性；接近截止、简并或模式交叉时仍需检查模场重叠。

## FDE区域与有效折射率

FDE在整个选定的x-y横截面求Maxwell本征解，不只在silicon内部求解。silicon、
sapphire、gas、有限计算窗口和边界条件都会影响本征场与neff：

$$
n_{\mathrm{eff}}=\frac{\beta}{k_0}.
$$

对上方空气、下方蓝宝石的非对称SOS波导，普通束缚导模通常需要满足：

$$
\max(n_{\mathrm{air}},n_{\mathrm{sapphire}})
<n_{\mathrm{eff}}<n_{\mathrm{Si}}.
$$

当前常数折射率模型中即为：

$$
1.67<n_{\mathrm{eff}}<3.42.
$$

空气没有被忽略；蓝宝石折射率更高，所以它给出更严格的下限。若neff低于蓝宝石
折射率，模式可能在空气侧衰减，却能向蓝宝石侧形成传播或泄漏场。

FDE窗口也是数值收敛参数。扩大窗口时应保持相近网格步长，再比较同一模式的neff、
模场和EFR。若场在边界处仍明显、或扩大窗口后指标变化显著，原窗口不够大。

## 2014 SOS结构扩展：strip、rib与slot

三种名称描述横截面，SOS描述材料平台：

| 结构 | 横截面含义 |
|---|---|
| strip | 单个全刻蚀矩形硅芯 |
| rib | 中央硅脊，两侧保留较薄的silicon slab |
| slot | 两条硅轨之间形成窄空气槽 |

quasi-TE与quasi-TM是模式家族，不是新的截面结构。

已完成的slot单点采用：

$$
W=2.0~\mu\mathrm m,\qquad S=0.3~\mu\mathrm m,\qquad H=0.6~\mu\mathrm m.
$$

其中W是两条硅轨外边缘之间的总宽，S是中央槽宽。每条硅轨宽度和中心位置为：

$$
W_{\mathrm{rail}}=\frac{W-S}{2}=0.85~\mu\mathrm m,
\qquad
x_{\mathrm{rail}}=\pm\frac{W+S}{4}=\pm0.575~\mu\mathrm m.
$$

本征模结果为neff=1.817729、TE fraction约95%。主导电场在中央槽增强，是因为法向
电位移在silicon/air界面满足连续条件；空气介电常数更低，所以空气侧法向电场更强。
色彩图的局部亮度仍不能替代纵向Poynting功率积分。

slot的气体掩膜必须排除两条硅轨，并把中央槽和硅轨外侧空气都计入分子。当前结果：

| 区域 | 占总模式功率 |
|---|---:|
| 中央slot | 9.87771% |
| 槽外气体 | 10.93021% |
| 全部气体 | 20.80792% |

中央槽面积很小，却贡献了全部气体功率的约47.47%。这说明槽增强确实显著，同时也
说明“槽内最亮”不等于“大部分总功率都在槽内”。

## slot二维参数扫描

slot设计可写成二维响应：

$$
\mathrm{EFR}=f(W,S).
$$

固定W扫描S，或固定S扫描W，都是在观察该二维曲面的一条截线。每个参数点都要重新
更新两条硅轨、气体掩膜并追踪同一条quasi-TE分支。工程目标不是只取最大EFR，还需
同时检查：

$$
n_{\mathrm{eff}}>n_{\mathrm{sapphire}},
$$

以及TE fraction、模场约束、截止/泄漏风险和工艺可实现性。学习顺序先完成2014 SOS
论文中的strip、slot和rib/slab，再进入U02 SNS。

## MODE GUI 最小重建

1. 新建 MODE/FDE 工程，传播方向设为 z；
2. 在 x-y 截面建立 sapphire 基底、矩形 Si 核心和气体区；
3. 设置 4.23 µm，求至少 4 个候选模；
4. 联合 neff、TE fraction 和模场选择 quasi-TE0；
5. 分别积分气体区和全截面的纵向 Poynting 功率；
6. 保存为独立学习副本，不覆盖原工程。

## 亲手任务

| W (µm) | 所选模式 | neff | TE fraction | EFR | 连续性依据 |
|---:|---|---:|---:|---:|---|
| 0.9 | quasi-TE0 | 1.76837 | 92.84% | 13.0774% | neff、偏振与单峰场连续 |
| 1.0 | quasi-TE0 | 1.94842 | 93.92% | 10.2300% | 基准锚点 |
| 1.1 | quasi-TE0 | 2.09472 | 95.33% | 8.91272% | neff、偏振与单峰场连续 |

## 常见误区

- 把 TE fraction 当成 TE0 透射率；
- 认为空气中有场就代表横向泄漏；
- 用场强平方积分冒充 Poynting EFR；
- 对均匀 10 cm 波导重复求大量相同截面。

## 验收问题

1. 为什么均匀 10 cm 直波导只需一次 FDE？
2. 宽度扫描时为什么不能只说“始终选 mode1”？
3. Poynting EFR 的分子和分母是什么？
4. 为什么空气中有倏逝场不等于横向泄漏？
5. 为什么SOS导模的neff下限由蓝宝石而不是空气决定？
6. slot中为什么中央槽最亮，却不能直接把亮度当作槽内功率占比？
7. 为什么二维扫描得到最大EFR后仍要检查截止、泄漏与工艺约束？

