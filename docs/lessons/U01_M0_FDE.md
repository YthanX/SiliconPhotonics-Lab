# U01：M0 FDE、本征模与 Poynting EFR

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
S_z=\frac12\operatorname{Re}(\mathbf E\times\mathbf H^*)\cdot\hat{\mathbf z}.
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
| 0.9 |  |  |  |  |  |
| 1.0 |  |  |  |  |  |
| 1.1 |  |  |  |  |  |

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

