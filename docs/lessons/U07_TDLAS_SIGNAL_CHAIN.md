# U07：Beer–Lambert、ADC、DAS 与 WMS/2f

> 状态：学习记录版（算法闭环已整理；真实硬件实验尚未接入）

本单元把 M4 的光学量接到 TDLAS（可调谐二极管激光吸收光谱）信号链：

```text
Gamma、有效光程 L
        → Beer–Lambert 吸收
        → PD/TIA 电压
        → ADC 码流
        → DAS 或 WMS/2f
        → 浓度标定与反演
```

本页记录的是算法和数据接口。当前项目中的 Python 脚本产生的是可重复的合成 ADC
波形，用来验证处理链；它们不是实际采集卡驱动，也不能证明样机精度或论文 LOD。

## 1. 核心问题

怎样从空白和含气探测器波形得到浓度，同时不把固定耦合/传播损耗、气体吸收和噪声
混成同一个参数？

沿非均匀波导的气体项可以写成：

```text
P_C(v) / P_0(v)
  = exp[- C * alpha_gas(v) * integral(Gamma(s) ds)]
```

`P0` 是同一光路的无气体基线，不是“空白吸收系数”。稳定的输入功率形状、耦合、
传播和固定增益在比值中可以近似抵消，但它们仍会影响绝对功率、SNR 和检测限。

## 2. DAS：直接吸收光谱

DAS（Direct Absorption Spectroscopy，直接吸收光谱）直接观察含气体后透射光的下降。

### 数据流

```text
blank ADC 码 P0[n] + gas ADC 码 P[n]
        → 码值换算为电压
        → T[n] = P[n] / P0[n]
        → A[n] = -ln(T[n])
        → 用吸收线模板拟合 C
```

最小实现的核心只有三步：

```python
blank_v = adc_decode(blank_codes, bits=16, full_scale_v=2.5)
gas_v = adc_decode(gas_codes, bits=16, full_scale_v=2.5)
transmission = gas_v / blank_v
absorbance = -np.log(transmission)
```

若模板已经包含 `Gamma * alpha_gas(v) * L`，则对 `absorbance` 的比例系数拟合就是
浓度估计；实际系统还需处理波长标定、基线漂移、饱和和噪声。

### DAS 的具体代码

上游项目中的最小教学脚本是：

`study_runs/U07/u07_das_from_adc.py`

它实现了：

- ADC 码到电压的换算；
- 含气/空白比值；
- `-ln(P/P0)` 吸光度；
- 吸收模板最小二乘拟合；
- 合成 ADC 闭环和单元测试。

更完整的联合脚本是：

`scripts/04_signal/run_m4_adc_concentration_loop.py`

它还加入了扫描、传播损耗、ADC 量化和噪声模型。

## 3. WMS/2f：波长调制光谱

WMS（Wavelength Modulation Spectroscopy，波长调制光谱）在慢速波长扫描上叠加高速
调制，不直接依赖总光强凹陷，而是提取吸收信号的谐波分量。

当前算法链为：

```text
5 Hz 慢扫描 + 2 kHz 调制
        → 探测器原始波形
        → 4 kHz（二次谐波）参考
        → I/Q 正交解调
        → FIR-Kaiser 低通
        → 相位校正
        → 2f 指标 M2f
        → M2f = k*C + b 标定
        → C = (M2f - b) / k
```

### 逐步对应代码

1. **产生调制失谐量**

   ```python
   fast_phase = 2 * np.pi * f_mod * t
   detuning = slow_detuning + modulation_index * np.sin(fast_phase)
   ```

2. **用 Beer–Lambert 生成探测器信号**

   ```python
   gas_exponent = Gamma * sigma * density * concentration * length_cm
   voltage = baseline * np.exp(-gas_exponent)
   ```

3. **用 2f 参考做 I/Q 解调**

   调制频率为 `2 kHz`，所以参考频率为 `4 kHz`：

   ```python
   reference_phase = 2 * fast_phase + electronics_phase
   I = 2 * voltage * np.cos(reference_phase)
   Q = 2 * voltage * np.sin(reference_phase)
   ```

4. **低通并合成幅值**

   ```python
   I2f = fir_filter(I)
   Q2f = fir_filter(Q)
   amplitude_2f = np.hypot(I2f, Q2f)
   ```

   当前重建采用论文公开的 50 Hz 通带、240 Hz 阻带设置。相位校正后取有效扫描段
   的峰峰值作为 `M2f`，再用标准浓度建立线性标定。

### WMS 的具体代码

完整降阶脚本是：

`scripts/04_signal/run_m4_wms_pipeline.py`

它会生成扫描、探测器、I/Q、相位校正 2f 波形和浓度标定表。论文算法文字对应的
公开学习证据包括：预处理、有效段切分、正交解调、FIR-Kaiser 滤波和相位校正。

## 4. DAS 与 WMS 的区别

| 问题 | DAS | WMS/2f |
|---|---|---|
| 主要输入 | 空白与含气波形 | 含调制的探测器波形和同步参考 |
| 中间量 | `P/P0`、`-ln(P/P0)` | `I2f`、`Q2f`、相位校正后的 `M2f` |
| 反演方式 | 直接拟合吸光度模板 | 用标准气体标定 `M2f-kC` |
| 复杂度 | 较低 | 较高，需要同步与相位处理 |
| 主要优点 | 物理含义直观 | 对部分低频基线和噪声更不敏感 |
| 当前代码状态 | 合成 ADC 闭环 | 合成调制/解调闭环 |

一句话：DAS 先算“含气/空白”，WMS 先“加调制并锁相提取 2f”。二者最后都需要
把光学吸收量映射为浓度。

## 5. 真实硬件边界

当前代码不是硬件采集程序。真实接入时，DAS 至少需要将模拟生成的
`blank_codes`、`gas_codes` 换成采集卡输出；WMS 需要将模拟 `voltage` 换成真实
ADC 波形，并保证 ADC 时钟、2 kHz 调制源和参考相位可追溯同步。

还没有接入的项目量包括：真实甲烷谱线和线宽、PD/TIA 增益、ADC 驱动、实测噪声与
漂移、标准气体标定及 Allan 检测限。因此合成脚本的拟合度或反演误差不能写成真实
样机性能。

## 6. 本轮学习记录

- 用户要求：把 DAS 和 WMS 分开讨论，并给出具体实验/算法代码入口。
- 本轮整理结论：两条链都有可运行的合成教学代码；DAS 的最小入口是
  `u07_das_from_adc.py`，WMS 的完整入口是 `run_m4_wms_pipeline.py`。
- 已明确边界：当前是离线信号链重建，不是实际 ADC、DFB、PD/TIA 或气室实验。
- 待继续：逐函数阅读 WMS 脚本，并把合成输入接口替换为真实 ADC 文件格式。

## 7. 验收问题

1. DAS 为什么需要空白波形？`P/P0` 中哪些因素近似抵消，哪些因素仍影响 SNR？
2. WMS 中为什么是 `2 kHz` 调制却使用 `4 kHz` 参考？
3. I/Q 解调、低通滤波和相位校正分别解决什么问题？
4. 当前脚本为什么能叫“算法实验”，却不能叫“真实硬件实验”？
