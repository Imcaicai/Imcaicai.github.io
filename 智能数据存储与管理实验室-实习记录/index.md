# 智能数据存储与管理实验室-实习记录


这里记录了在智能数据存储与管理实验室的实习经历。前部分是关于【深度学习】、【hash】的内容；后部分主要和【index】相关。



## 2022

**【2022.10】**

- 看论文： ***Vision GNN: An Image is Worth Graph of Nodes*** 

  🔗 [阅读笔记](https://imcaicai.github.io/an-image-is-worth-graph-of-nodes/)

- 参与论文修改： ***Supervised Hierarchical Online Hashing for Cross-modal Retrieval***

  PS：主要是帮学长找语法错误、可以改进的地方

**【2022.11】**

- 看论文： ***Label Embedding Online Hashing for Cross-Modal Retrieval*** 

  🔗 [阅读笔记](https://imcaicai.github.io/lemon/)

- 参与论文修改： ***Supervised Hierarchical Online Hashing for Cross-modal Retrieval***


**【2022.12】**

- 参与论文修改： ***Supervised Hierarchical Online Hashing for Cross-modal Retrieval***




------



## 2023

**【2023.04】**

- 看论文：  ***Magic mirror in my hand, which is the best in the land?An Experimental Evaluation of Index Selection Algorithms***  

  🔗 [阅读笔记](https://imcaicai.github.io/an-experimental-evaluation-of-index-selection-algorithms/)

  重点：文章中介绍了现在比较流行的几种针对关系性数据库的索引推荐方法，熟悉其实验平台和相关背景知识。

- 看论文：  ***Slalom: Coasting Through Raw Data via Adaptive Partitioning and Indexing***

  🔗 [阅读笔记](https://imcaicai.github.io/slalom-coasting-through-raw-data-via-adaptive-partitioning-and-indexing/)

  重点：文章介绍了一个列存大数据分析引擎，里面提及到的partition是这种场景的核心。重点是理解parition上的索引和partition内部的索引有什么区别。

**【2023.05】**

- 看论文：   ***Access Path Selection in Main-Memory Optimized Data Systems: Should I Scan or Should I Probe?*** 

  🔗 [阅读笔记](https://imcaicai.github.io/access-path-selection-in-main-memory-optimized-data-systems/)

  重点：文章介绍了如何去评估索引价值的方法，这是whatif功能探索的核心。

- 看论文：   ***SWIRL: Selection of Workload-aware Indexes using Reinforcement Learning*** 

  🔗 [阅读笔记](https://imcaicai.github.io/swirl/)

  重点：了解一种基于强化学习的索引推荐方法 SWIRL，这是我们目前参考的方法。

- 配环境

  🔗 [ubuntu安装greenplum教程及踩坑记录](https://imcaicai.github.io/ubuntu%E5%AE%89%E8%A3%85greenplum%E6%95%99%E7%A8%8B%E5%8F%8A%E8%B8%A9%E5%9D%91%E8%AE%B0%E5%BD%95/)

- 看 SWIRL 的代码

  （穿插了五一假期、2门考试、CSP及一个比赛的答辩，进度稍微有点慢）

**【2023.06】**

- 配环境、调试运行 swirl 的代码

  🔗 [cloudlab+greenplum+python3配置](https://imcaicai.github.io/cloudlab-greenplum-python3%E9%85%8D%E7%BD%AE/)

- 补充 swirl 的代码，实现谓词分割、获取分区信息的功能

  （结课考试周+旅游，这个月有点水）

**【2023.07】**

- 对改进后的 swirl 调参数，获取较好的实验效果
- 学习 StableBaselines3 强化学习框架
- 参加计算机系统能力大赛——数据库管理系统设计赛

