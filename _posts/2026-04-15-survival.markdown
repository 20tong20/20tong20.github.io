## 生存分析（Survival Analysis）
是统计学中一个专门研究“事件发生时间”的领域，其数据（称为生存数据或失效时间数据）有两个关键特征：

1.  **事件时间（Time-to-Event）**：我们关心的结果是一个非负随机变量，表示从某个起点到某个“事件”发生所经过的时间。
2.  **删失（Censoring）**：由于各种原因（如研究结束、失访），我们可能无法观测到所有研究对象的完整事件时间。


---

### 1. 核心数学函数

设 $T$ 为一个非负连续随机变量，表示从时间起点（$t=0$）到事件发生的“失效时间”（Failure Time）。

#### 1.1 生存函数 (Survival Function)

生存函数 $S(t)$ 定义为个体存活时间 $T$ 超过 $t$ 的概率。

$$S(t) = P(T > t)$$

**性质**：
* $S(0) = P(T > 0) = 1$ （在研究开始时，所有个体都“存活”）。
* $\lim_{t \to \infty} S(t) = 0$ （假设事件最终会发生）。
* $S(t)$ 是一个非增函数（non-increasing）。

#### 1.2 概率密度函数 (Probability Density Function, PDF)

如果 $T$ 是连续的，其累积分布函数 (CDF) $F(t) = P(T \le t) = 1 - S(t)$。
其概率密度函数 $f(t)$ 定义为：

$$f(t) = \frac{d}{dt} F(t) = \lim_{\Delta t \to 0} \frac{P(t \le T < t + \Delta t)}{\Delta t}$$

因此，我们有 $f(t) = -\frac{d}{dt} S(t)$。

#### 1.3 风险函数 (Hazard Function)

风险函数 $h(t)$（也常记为 $\lambda(t)$）衡量的是在 $t$ 时刻“存活”的个体，在下一个极小时间间隔 $(t, t+\Delta t]$ 内发生事件的“瞬时速率”。

$$h(t) = \lim_{\Delta t \to 0} \frac{P(t \le T < t + \Delta t \mid T \ge t)}{\Delta t}$$

**推导**：
根据条件概率的定义：
$$h(t) = \lim_{\Delta t \to 0} \frac{P(t \le T < t + \Delta t)}{P(T \ge t) \cdot \Delta t} = \frac{1}{S(t)} \lim_{\Delta t \to 0} \frac{F(t + \Delta t) - F(t)}{\Delta t}$$
$$h(t) = \frac{f(t)}{S(t)}$$

$h(t)$ 是生存分析的核心，因为它描述了风险随时间的变化模式。

#### 1.4 累积风险函数 (Cumulative Hazard Function)

累积风险函数 $H(t)$ 是风险函数从 0到 $t$ 的积分，代表到时间 $t$ 为止累积的总风险。

$$H(t) = \int_0^t h(u) du$$

#### 1.5 函数间的关系

这四个函数在数学上是等价的，知道其中一个就可以推导出其他三个：

1.  $h(t) = \frac{f(t)}{S(t)}$
2.  $H(t) = \int_0^t h(u) du \implies h(t) = \frac{d}{dt} H(t)$
3.  $f(t) = -\frac{d}{dt} S(t)$
4.  **关键关系**：
    $$H(t) = \int_0^t \frac{f(u)}{S(u)} du = \int_0^t \frac{-S'(u)}{S(u)} du = -[\ln S(u)]_0^t = -(\ln S(t) - \ln S(0))$$
    由于 $S(0)=1$, $\ln S(0) = 0$，所以：
    $$H(t) = -\ln S(t)$$
    因此，
    $$S(t) = \exp(-H(t)) = \exp\left(-\int_0^t h(u) du\right)$$

---

### 2. 删失 (Censoring)

删失是生存数据的主要特征。最常见的是**随机右删失 (Random Right Censoring)**。

对于第 $i$ 个个体，我们有两个随机变量：
* $T_i$：真实的失效时间。
* $C_i$：真实的删失时间。

我们假设 $T_i$ 和 $C_i$ 是相互独立的（非信息性删失 Non-informative Censoring）。

在研究中，我们**观测**到的是：
1.  观测时间 $X_i = \min(T_i, C_i)$
2.  事件指示变量 $\delta_i = I(T_i \le C_i)$（$I(\cdot)$ 为示性函数）
    * $\delta_i = 1$ 表示观测到事件（$T_i \le C_i$）
    * $\delta_i = 0$ 表示发生删失（$T_i > C_i$）

观测数据为 $n$ 组 $(x_i, \delta_i)$。

#### 2.1 似然函数 (Likelihood Function)

构建似然函数是所有参数和半参数模型的基础。
对于第 $i$ 个观测 $(x_i, \delta_i)$：

* **如果 $\delta_i = 1$ (事件发生)**：
    这意味着 $T_i = x_i$ 且 $C_i \ge x_i$。该观测的似然贡献是 $T_i$ 在 $x_i$ 处的概率密度：
    $L_i = P(T_i = x_i) = f(x_i)$
    （严格来说，是 $P(T_i \in [x_i, x_i+dx]) = f(x_i)dx$）

* **如果 $\delta_i = 0$ (删失发生)**：
    这意味着 $C_i = x_i$ 且 $T_i > x_i$。该观测的似然贡献是 $T_i$ 存活超过 $x_i$ 的概率：
    $L_i = P(T_i > x_i) = S(x_i)$

**总似然函数 (Likelihood)**：
$$L = \prod_{i=1}^n L_i = \prod_{i=1}^n [f(x_i)]^{\delta_i} [S(x_i)]^{1-\delta_i}$$

利用 $f(t) = h(t)S(t)$，我们可以将其重写为以风险函数为中心的形式：
$$L = \prod_{i=1}^n [h(x_i) S(x_i)]^{\delta_i} [S(x_i)]^{1-\delta_i} = \prod_{i=1}^n [h(x_i)]^{\delta_i} S(x_i)$$

再利用 $S(t) = \exp(-H(t))$：
$$L = \prod_{i=1}^n [h(x_i)]^{\delta_i} \exp(-H(x_i))$$
这个形式在参数建模（如威布尔分布）中非常有用。

---

### 3. 非参数估计

当 $S(t)$ 的函数形式未知时使用。

#### 3.1 Kaplan-Meier (KM) 估计 (乘积极限估计)

***容易出现维度诅咒，在预测变量较小时表现好***
KM 估计用于估计生存函数 $S(t)$。
设 $t_1 < t_2 < \dots < t_k$ 是 $n$ 个样本中观测到的 $k$ 个**唯一**的事件时间点。

* $n_j$：在 $t_j$ 时刻之前（即 $t_j^-$）仍然存活且未被删失的个体数（**风险集 Risk Set**，$n_j = |\{i: x_i \ge t_j\}|$）。
* $d_j$：在 $t_j$ 时刻**发生**事件的个体数。
* $c_j$：在 $t_j$ 时刻**删失**的个体数（或在 $[t_j, t_{j+1})$ 区间内删失）。

在 $t_j$ 时刻，事件发生的条件概率估计为 $\frac{d_j}{n_j}$。
在 $t_j$ 时刻，存活的条件概率估计为 $1 - \frac{d_j}{n_j}$。

Kaplan-Meier 估计 $S(t)$ 是这些条件概率的乘积：
$$\hat{S}_{KM}(t) = \prod_{j: t_j \le t} \left( 1 - \frac{d_j}{n_j} \right)$$
这是一个阶梯函数，仅在观测到事件的时间点 $t_j$ 发生跳跃。

#### 3.2 Nelson-Aalen (NA) 估计

NA 估计用于估计累积风险函数 $H(t)$。
它基于在 $t_j$ 时刻的瞬时风险 $\hat{h}(t_j) = \frac{d_j}{n_j}$。
NA 估计是这些瞬时风险的累加：
$$\hat{H}_{NA}(t) = \sum_{j: t_j \le t} \frac{d_j}{n_j}$$

（注：$\hat{H}_{NA}(t) \approx -\ln(\hat{S}_{KM}(t))$，因为 $\ln(1-x) \approx -x$）

---

### 4. 假设检验：Log-rank 检验

Log-rank 检验是一种非参数检验，用于比较 $G$ 组（例如，治疗组 vs 对照组）的生存曲线是否显著不同。

**原假设 (Null Hypothesis)**：
$H_0: S_1(t) = S_2(t) = \dots = S_G(t)$ （所有组的生存函数相同）
（等价于 $H_0: h_1(t) = h_2(t) = \dots = h_G(t)$）

**检验统计量**：
以 $G=2$ 为例（组1，组2）。
在 $H_0$ 下，两组的风险应该相同。
在每个事件时间点 $t_j$：
* 总风险集 $n_j = n_{1j} + n_{2j}$
* 总事件数 $d_j = d_{1j} + d_{2j}$
* $O_{1j} = d_{1j}$ 是组1在 $t_j$ 时的**观测事件数**。
* 在 $H_0$ 下，组1的**期望事件数** $E_{1j}$（基于超几何分布）为：
    $$E_{1j} = d_j \cdot \frac{n_{1j}}{n_j}$$

Log-rank 检验比较总的观测事件数和期望事件数：
$$U = \sum_{j=1}^k (O_{1j} - E_{1j})$$

该统计量 $U$ 的方差 $V$ 也有一个（较复杂的）闭式解 $V = \sum_{j=1}^k \frac{n_{1j} n_{2j} d_j (n_j - d_j)}{n_j^2 (n_j - 1)}$。

检验统计量 $Z^2 = \frac{U^2}{V}$ 在 $H_0$ 下近似服从 $\chi^2_1$ 分布（自由度为1）。

---

### 5. 回归模型 (Regression Models)

当需要分析协变量 $\boldsymbol{Z} = (Z_1, \dots, Z_p)^T$ 对生存时间的影响时，使用回归模型。
![](\assets\survival.png)
Note that both the proportional hazards model and the proportional odds model are two special cases of linear transformation model (Zeng and Lin 2007) that relates an unknown transformation of the failure time T to the covariates X.
**("C:\Users\tongt\Zotero\storage\IJWH5LLM\Xia 等 - 2010 - Dimension reduction and semiparametric estimation of survival models.pdf")**

#### 5.1 Cox 比例风险模型 (Cox Proportional Hazards, PH)

Cox 模型是应用最广泛的半参数模型。

**模型设定**：
它假设协变量 $\boldsymbol{Z}$ 对风险函数 $h(t)$ 的影响是**乘法**形式，且**不随时间变化**。
$$h(t \mid \boldsymbol{Z}) = h_0(t) \cdot \exp(\boldsymbol{\beta}^T \boldsymbol{Z})$$
$$h(t \mid \boldsymbol{Z}) = h_0(t) \exp(\beta_1 Z_1 + \dots + \beta_p Z_p)$$

* $h_0(t)$：**基线风险函数 (Baseline Hazard)**。这是当所有 $\boldsymbol{Z} = \boldsymbol{0}$ 时的风险函数。它是非参数的，形式未知。
* $\boldsymbol{\beta} = (\beta_1, \dots, \beta_p)^T$：回归系数向量。

**比例风险 (Proportional Hazards) 假设**：
取两个不同协变量 $\boldsymbol{Z}_1$ 和 $\boldsymbol{Z}_2$ 的个体，其风险比 (Hazard Ratio, HR) 为：
$$HR = \frac{h(t \mid \boldsymbol{Z}_1)}{h(t \mid \boldsymbol{Z}_2)} = \frac{h_0(t) \exp(\boldsymbol{\beta}^T \boldsymbol{Z}_1)}{h_0(t) \exp(\boldsymbol{\beta}^T \boldsymbol{Z}_2)} = \exp(\boldsymbol{\beta}^T (\boldsymbol{Z}_1 - \boldsymbol{Z}_2))$$
该 $HR$ 是一个常数，**不随时间 $t$ 变化**。这就是“比例风险”的含义。

**参数估计：偏似然 (Partial Likelihood)**
由于 $h_0(t)$ 未知，我们无法使用第2节中的全似然函数。Cox (1972) 提出了偏似然方法。

设 $t_1 < t_2 < \dots < t_k$ 是 $k$ 个**唯一**的事件时间（为简化，假设无并列事件）。
* $\mathcal{R}(t_j) = \{i: X_i \ge t_j\}$ 是在 $t_j$ 时的风险集。
* 设 $i_j$ 是在 $t_j$ 时刻**发生事件**的那个个体。

在 $t_j$ 时刻，给定风险集中有一个事件发生，这个事件恰好发生在 $i_j$ 身上的条件概率是：
$$P(i=i_j \mid \text{event at } t_j, \mathcal{R}(t_j)) = \frac{P(i_j \text{ fails at } t_j)}{\sum_{l \in \mathcal{R}(t_j)} P(l \text{ fails at } t_j)}$$
$$\approx \frac{h(t_j \mid \boldsymbol{Z}_{i_j})}{\sum_{l \in \mathcal{R}(t_j)} h(t_j \mid \boldsymbol{Z}_l)} = \frac{h_0(t_j) \exp(\boldsymbol{\beta}^T \boldsymbol{Z}_{i_j})}{\sum_{l \in \mathcal{R}(t_j)} h_0(t_j) \exp(\boldsymbol{\beta}^T \boldsymbol{Z}_l)}$$
$h_0(t_j)$ 项被约掉了！
$$PL_j(\boldsymbol{\beta}) = \frac{\exp(\boldsymbol{\beta}^T \boldsymbol{Z}_{i_j})}{\sum_{l \in \mathcal{R}(t_j)} \exp(\boldsymbol{\beta}^T \boldsymbol{Z}_l)}$$

**总偏似然函数**：
$$L_{partial}(\boldsymbol{\beta}) = \prod_{j=1}^k PL_j(\boldsymbol{\beta}) = \prod_{j=1}^k \frac{\exp(\boldsymbol{\beta}^T \boldsymbol{Z}_{i_j})}{\sum_{l \in \mathcal{R}(t_j)} \exp(\boldsymbol{\beta}^T \boldsymbol{Z}_l)}$$

我们通过最大化 $L_{partial}(\boldsymbol{\beta})$ 来估计 $\boldsymbol{\beta}$（通常使用 Newton-Raphson 算法）。

#### 5.2 加速失效时间模型 (Accelerated Failure Time, AFT)

AFT 模型是另一类模型，它是参数模型，直接对 $\log(T)$ 建模。

**模型设定**：
$$\log(T_i) = \boldsymbol{\beta}^T \boldsymbol{Z}_i + \sigma \epsilon_i$$
其中 $\epsilon_i$ 是来自某个特定分布（如 Gumbel, Normal, Logistic）的误差项，$\sigma$ 是尺度参数。

**解释**：
AFT 假设协变量 $\boldsymbol{Z}$ 对 $T$ 具有**乘法**效应，即“加速”或“减速”了失效时间。
$$T_i = \exp(\boldsymbol{\beta}^T \boldsymbol{Z}_i) \cdot \exp(\sigma \epsilon_i)$$
$\exp(\beta_j)$ 被称为“时间比率”（Time Ratio）。
例如，如果 $\beta_j = 0.693$（即 $\exp(\beta_j) \approx 2$），则 $Z_j$ 增加1个单位，会导致预期失效时间变为原来的2倍（即存活更长）。

这与 Cox 模型（风险比）的解释完全不同。AFT 模型使用第2节中的**全似然函数**（需要指定 $\epsilon$ 的分布）进行估计。
