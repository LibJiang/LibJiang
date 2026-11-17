# LibJiang

<a href="https://github.com/LibJiang">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=16&duration=2800&pause=900&color=00A86B&center=true&vCenter=true&width=760&height=100&lines=%24+whoami+%E2%86%92+Jade+Shark;%24+focus+%E2%86%92+PQC+%C2%B7+CIM+%C2%B7+Accelerator;%24+school+%E2%86%92+NJU+%C2%B7+%E9%9B%86%E6%88%90%E7%94%B5%E8%B7%AF;%24+motto+%E2%86%92+%E9%A3%8E%E9%9B%A8%E5%85%BC%E7%A8%8B" alt="Dynamic terminal profile" />
</a>

**后量子密码硬件 · 存算一体 · 3D 垂直集成 · 数字设计**

[![南京大学 · 集成电路](https://img.shields.io/badge/%E5%8D%97%E4%BA%AC%E5%A4%A7%E5%AD%A6-%E9%9B%86%E6%88%90%E7%94%B5%E8%B7%AF-00A86B?style=flat-square)](https://www.nju.edu.cn/)
[![Verilog](https://img.shields.io/badge/-Verilog-1C6B9C?style=flat-square)](https://github.com/LibJiang?tab=repositories&language=verilog)
[![C/C++](https://img.shields.io/badge/-C%2FC%2B%2B-00599C?style=flat-square&logo=cplusplus&logoColor=white)](https://github.com/LibJiang)
[![Python](https://img.shields.io/badge/-Python-3776AB?style=flat-square&logo=python&logoColor=white)](https://github.com/LibJiang)
[![PyTorch](https://img.shields.io/badge/-PyTorch-EE4C2C?style=flat-square&logo=pytorch&logoColor=white)](https://github.com/LibJiang)
[![Vivado](https://img.shields.io/badge/-Vivado-B33A3A?style=flat-square)](https://github.com/LibJiang)
[![Email](https://img.shields.io/badge/Email-1920837253%40qq.com-00A86B?style=flat-square&logo=maildotru&logoColor=white)](mailto:1920837253@qq.com)

---

## 关于我

我是 **Jade Shark（LibJiang）**，南京大学集成电路（微电子）方向本科生。

我的兴趣落在**体系结构**与**芯片实现**之间那条缝里：往上，关心怎么把算法压成硬件；往下，关心晶体管与互联还能怎么叠。

手上有两个大创。一个是我作为**第一负责人**的 **HQC-KEM-FPGA/ASIC**：把抗量子密码 HQC-128 的密钥封装机制用完整 RTL 实现，用官方 KAT 逐字节对拍，目标是把 FPGA 全功能跑通并推进到多工艺 ASIC 综合（田静老师课题组方向）。另一个是我作为**架构主力**的**垂直存算加速器**：让存储与计算叠在一起，少搬一点数据。

写 Verilog，写 C/C++，也用 Python 和 PyTorch 做建模、仿真与验证。相信"能跑通的架构图"才算架构图。

> 风雨兼程。

---

## 研究方向：让计算贴着数据长大

传统架构里，数据要跑很远才能被算一次；我想把这段路拆掉。

```text
计算层  ┌──────────────────────────────┐
        │  Reconfigurable PE Array     │
        └──────────────┬───────────────┘
                       │ 垂直互联 ① · CFET SRAM-PE
        ┌──────────────┴───────────────┐
        │  3D DRAM                     │
        └──────────────────────────────┘
存储层
```

- **第一级垂直连接**：CFET SRAM-PE，把存储单元直接长在计算单元上方，缩短访存路径。
- **第二级垂直连接**：3D DRAM-Core，把主存做成"楼"，让带宽随面积一起增长。
- **可重构性**：同一套 PE 阵列，在不同负载下切换数据流与控制流，避免为单一算子做死。
- **数据生命周期优化**：从产生、复用、驻留到淘汰，全链路决定数据"该住在哪一层"，而不是搬来搬去。

## 数据生命周期视角下的架构闭环

```mermaid
flowchart LR
    W["工作负载 / Workload"] --> A{"架构探索"}
    A -->|"映射到 DRAM-Core"| C["3D DRAM 侧数据流"]
    A -->|"映射到 SRAM-PE"| D["CFET SRAM-PE 侧数据流"]
    C --> E["数据生命周期优化<br/>产生 · 复用 · 驻留 · 淘汰"]
    D --> E
    E --> F["可重构多核调度"]
    F --> G["Verilog RTL 实现"]
    G --> H["仿真 / 面积-功耗-性能评估"]
    H -.->|"反馈迭代"| A
```

---

## 精选项目

### 1. [dachuang · HQC-KEM-FPGA/ASIC](https://github.com/LibJiang/dachuang)

用**完整 RTL** 实现 **HQC-128 KEM**（KeyGen / Encaps / Decaps + FO + 隐式拒绝），用官方 KAT 逐字节验证；不只"能跑"，要做成可交付、可复现的 IP。

- **架构**：三阶段共享 SHAKE256/Keccak · 低延迟 dense×sparse 多项式乘法 · ePIBM RS / FHT RM 级联解码 · 固定重量采样 · 恒定时间比较
- **已跑通**：官方 KAT **100/100**（pk/sk/ss）· C 黄金 **5/5** · `sparse_qc_mult` 基线 N=17669 折叠 **831/831**
- **目标**：AC701 XC7A200T 板端全功能跑通 + PPA（2027-05）→ 40nm 主 / 55/28/90nm 可选 ASIC 综合，全程开源
- **我的角色**：**大创第一负责人**（4 人团队），负责进度、Vivado 平台与 FPGA bring-up、系统仿真、PPA、IP 打包与 release

`PQC` `HQC` `Verilog` `FPGA` `ASIC` `Cryptography`

### 2. [vs-accel-vertical-storage-compute-accelerator](https://github.com/LibJiang/vs-accel-vertical-storage-compute-accelerator-)

面向两级垂直存算连接（3D DRAM-Core、CFET SRAM-PE）的可重构多核加速器：架构探索与数据生命周期优化。**我的角色**：大创架构主力，研究容量 / 带宽 / 共享范围怎么搭配最划算，并总结出可复现的设计规律。

`Computer Architecture` `CIM` `3D-IC` `Reconfigurable Accelerator`

> 一条加密、一条存算：一个把密码学算进硅里，一个把存储叠进计算里。

---

## 技术版图

| 方向 | 技术与工具 |
| --- | --- |
| 密码学硬件 | 后量子密码（HQC KEM）· SHAKE256 / Keccak · RS(ePIBM) / RM(FHT) 编解码 · 恒定时间设计 · KAT 逐字节对拍 |
| 硬件描述与设计 | Verilog（RTL 设计 · 仿真验证）· AXI4-Lite · Vivado / xsim · iverilog / Verilator · 数字系统设计 |
| 体系结构 | 存算一体（CIM / Near-Memory Computing）· 3D 垂直集成 · 可重构多核架构 · 数据生命周期优化 |
| 编程语言 | C/C++（系统与底层 · 黄金模型）· Python（建模 · 仿真 · 数据处理）· Shell |
| 机器学习 | PyTorch（模型搭建 · 训练与推理）· 软硬件协同的算子/模型加速 |
| 工具与工程 | Synopsys DC（ASIC 综合）· Linux / WSL · Git / GitHub Actions · 论文复现与实验记录 |

---

## 现在进行时

| 状态 | 内容 |
| --- | --- |
| 主线 | 大创 HQC-KEM：官方 KAT 100/100 已过，正推进 M1 模块 RTL（共享 SHAKE / 固定重量采样 / dense×sparse / ePIBM / FHT） |
| 支线 | 垂直存算加速器架构探索（大创架构主力） |
| 在学 | 计算机体系结构、数字集成电路设计、密码学硬件与 ASIC 后端 |
| 想要 | 与做后量子密码硬件 / 存算 / 数字设计的人交流，找能一起把图画成硅的人 |

---

## GitHub 足迹

[![Public Repos](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fapi.github.com%2Fusers%2FLibJiang&query=%24.public_repos&label=Public%20Repos&color=00A86B&style=flat-square)](https://github.com/LibJiang?tab=repositories)
[![Followers](https://img.shields.io/github/followers/LibJiang?label=Followers&style=flat-square&color=00A86B)](https://github.com/LibJiang?tab=followers)
[![Stars](https://img.shields.io/github/stars/LibJiang?affiliations=OWNER&label=Stars&style=flat-square&color=00A86B)](https://github.com/LibJiang?tab=stars)
[![Last Commit](https://img.shields.io/github/last-commit/LibJiang/LibJiang?label=Profile%20Updated&style=flat-square&color=00A86B)](https://github.com/LibJiang/LibJiang/commits)

### 过去一年的 3D 参与度地形

<div align="center">
  <a href="https://github.com/LibJiang?tab=overview">
    <img src="https://raw.githubusercontent.com/LibJiang/LibJiang/main/profile-3d-contrib/profile-green-animate.svg" width="88%" alt="3D contribution calendar, rebuilt daily by GitHub Actions" />
  </a>
</div>

把一年的提交摊成一座等距地形：柱子越高，那天在键盘前坐得越久。
这张图由 GitHub Actions **每天自动重建**，不用手改 —— 我多写一行代码，它就自己长高一格。

<!--
  可选：夜间配色方案（想看星空版就把上面 img 的 src 换成下面这个文件名）
  profile-3d-contrib/profile-night-rainbow-animate.svg

  可选：静态版（不想动画时）
  profile-3d-contrib/profile-green.svg
-->


<!--
  可选：等仓库多起来以后，取消下面两段的注释即可启用贡献图卡片。
  注意 github-readme-stats 在国内访问可能较慢，也可以像 never-die-cold 那样用 Actions 自建。

  ![GitHub Stats](https://github-readme-stats.vercel.app/api?username=LibJiang&show_icons=true&hide_title=true&theme=vue&border_color=00A86B)

  ![Activity Graph](https://github-readme-activity-graph.vercel.app/graph?username=LibJiang&theme=vue&color=00A86B&line=00A86B&point=00A86B)
-->

---

## 联系

- GitHub：[LibJiang](https://github.com/LibJiang)
- 邮箱：[1920837253@qq.com](mailto:1920837253@qq.com)

**对后量子密码硬件、存算架构、可重构加速器感兴趣，或者想找人一起做数字设计 —— 欢迎来聊。**
