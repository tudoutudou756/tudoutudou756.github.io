# 机器学习交互演示中心

学期期末作业：一个纯静态、零依赖的机器学习算法交互演示站点。

## 运行方式

无需安装任何依赖，无需启动服务器：

- 直接双击打开 `index.html` 即可浏览全部演示
- 所有页面均为自包含 HTML（原生 JS + Canvas 实现），双击任意子页面也可独立打开
- 若浏览器因 file:// 协议限制出现异常，可在本目录启动任意静态服务器，例如：
  - `python -m http.server 8000` 然后访问 `http://localhost:8000`
  - 或 VS Code 安装 Live Server 插件后右键 index.html → Open with Live Server

## 页面结构

| 目录 | 文件 | 内容 | 核心交互 |
|------|------|------|----------|
| `./` | index.html | 导航首页（暖阳风卡片布局） | 卡片悬停动效 |
| `1evaluation/` | evaluation_demo.html | 模型评估 | 阈值滑块 ↔ 混淆矩阵 ↔ ROC 曲线三点联动，点击单元格查看 TP/FP/FN/TN 解释，实时计算 Accuracy/Precision/Recall/F1/AUC |
| `2regression/` | housing_visualization.html | 线性回归 | 手动拖滑块调 w/b 实时计算 MAE/MSE/R²，一键显示 OLS 解析解最优直线对比 |
| `3logistic/` | logistic_visualization.html | Logistic 回归 | 梯度下降训练（单步/200步），概率背景 + 决策边界随训练实时变化，损失曲线 |
| `4tree/` | decision_tree_visualization.html | 决策树 | 逐步执行 CART 基尼分裂，树结构与数据空间划分双视图联动，可调最大深度/最少样本观察过拟合 |
| `5nn/` | nn_visualization.html | 神经网络 | MLP(2-8-8-1) 反向传播训练，XOR/环形/双月牙三数据集切换，权重线粗细可视化，决策边界动画 |
| `6svm/` | svm_visualization.html | 支持向量机 | 线性核/RBF 核切换，间隔带与支持向量高亮，调 C 与 σ 观察过拟合/欠拟合（LSSVM 求解） |
| `7bayes/` | naive_bayes_visualization.html | 朴素贝叶斯 | 拖动特征滑块查看三类后验概率条实时变化，高斯似然曲线，置信度背景图（鸢尾花数据） |
| `8ensemble/` | ensemble_visualization.html | 集成学习 | Bagging 随机森林与 AdaBoost 并排对比，逐批添加学习器观察边界细化，样本权重可视化 |
| `9clustering/` | clustering_visualization.html | K-means 聚类 | 单步/自动执行 Lloyd 迭代，质心移动轨迹，SSE 收敛曲线 + 肘部法则自动计算 |
| `10dimension/` | dimension_reduction_visualization.html | PCA 降维 | 主成分方向箭头，1D 投影展示，方差解释率条，PC1 方向角/相关系数实时计算 |

## 图上公式条

每个演示的主画布下方都嵌着一条「公式条」（`class="formula-strip"`，共 19 条），实时显示**驱动该图变化的那条公式**以及**当场代入的参数值**，随滑块拖动 / 训练迭代同步刷新。例如：

- 线性回归：`ŷ = w·x + b` 里 w、b 的实时取值，以及 OLS 解析解 `w* = Σ(xᵢ−x̄)(yᵢ−ȳ)/Σ(xᵢ−x̄)²` 的结果
- Logistic / 神经网络：`z = W·a + b`、`σ(z)`、决策边界方程、反向传播 `δ⁽ˡ⁾` 与权重更新式
- 决策树：`Gini(t) = 1 − Σₖpₖ²`、`ΔGini` 增益与当前最优分裂的具体阈值
- SVM：`f(x) = ΣᵢαᵢK(xᵢ,x) + b`、核函数形式（含当前 σ）、LSSVM 线性系统与 C、支持向量数
- K-means：簇分配/质心更新式、`SSE` 目标值与肘部法则自动给出的 K

改动方式：编辑对应页面 `refresh` / `update` / `updateUI` 函数里给公式条 `innerHTML` 赋值的那段即可，无需改动绘图逻辑。

## 修改指南

- **改首页文案/卡片**：编辑 `index.html` 中 `demo-card` 区块，注意 `href` 与子目录文件名对应
- **改配色主题**：全站为「黄昏暖阳」暖色系——首页色值集中在 `:root` CSS 变量（`--primary-color: #d2761f` 等）；子页面主色为琥珀橙 `#d2761f`、赭金 `#a05f1c`、落日橘 `#e06a2e`，背景奶油色 `#faf2e3`，搜索对应十六进制色值即可。散点图中的分类色（红/绿/蓝等）为数据可读性保留，请勿统一
- **改数据分布**：每个子页面 `<script>` 开头都有数据生成函数（`makeData` / 数据初始化 IIFE），调整均值、方差、样本数即可
- **改算法参数**：演示中可调的参数均暴露为页面滑块；固定参数（如 MLP 结构 `SIZES = [2,8,8,1]`、树深度）在 script 顶部常量处修改
- **加新演示页**：新建子目录 `11xxx/xxx.html`，复制任意现有子页的「返回首页按钮 + 页脚」结构，然后在 index.html 加一张卡片

## 质量说明

- 全部页面零外部依赖（无 CDN、无字体、无图片外链），离线可用
- 桌面/移动端：主体布局使用 flex/grid 自动换行，768px 以下单列可读
- 所有图表为 Canvas 手绘，坐标轴、图例、刻度齐全
- 数值算法（OLS、梯度下降、CART、LSSVM 高斯消元、PCA 2x2 特征分解、K-means）均为手写实现，带边界保护（空簇、除零、数值下溢）
- 交互控件均有边界状态：滑块拖动实时反馈，按钮可重复点击，训练类按钮有重置入口
