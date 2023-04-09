---
title: 强化学习（Reinforcement Learning）
date: 2021-01-27 00:00:58
categories: 学习日志
tags:
  - 强化学习
  - 神经网络
  - Reinforcement Learning
  - Neural Network
mathjax: true
toc: true
---

## 0. **人工不智能，机器爱学习**

什么课题什么方向都拉倒吧，强化学习才是AI的主宰！！！

简单的做了个笔记，边学边补充。唉，就是玩：[强化学习玩小游戏](https://www.bilibili.com/video/BV14T4y1P7z9)

主要参考：[蘑菇书EasyRL](https://github.com/datawhalechina/easy-rl)、周志华机器学习、[莫烦Python](https://github.com/MorvanZhou/Reinforcement-learning-with-tensorflow)

<!--more-->

从强化学习的几个元素的角度划分，方法主要有下面几类：

- policy-based agent, 基于策略的智能体，找最优策略。
- value-based agent, 基于价值的智能体，找最优奖励总和。
- action-critic agent, 演员评论员智能体，策略和价值都最优。

然后又从不同的角度可以细分为：

- Model-free：不去理解环境，根据真实世界的反馈，采取下一步行动。
- Model-based：先理解理解环境，并建立一个虚拟环境，通过想象预判接下来要发生的所有情况，选择最好的情况作为下一步的策略。它比 Model-free 多出了虚拟环境和想象力。
- Policy based：通过感官分析所处的环境，得到下一步各种行为的概率，然后根据概率采取行动。
- Value based：输出的是所有动作的价值，根据最高价值来选动作，这类方法不能选取连续的动作。
- Monte-carlo update：结束时总结这一回合中的所有转折点，再更新行为准则。
- Temporal-difference update：环境中每一步都在更新，一直学习。
- On-policy：自身参与到环境中进行学习。
- Off-policy：可通过其他系统参与到环境得到的反馈进行学习。

## 1. **Q-Learning**

- **1、算法思想**

    Q-Learning属于value-based的算法，也是Off-policy，$Q$即为$Q(s,a)$就是在某一时刻的$s$状态下$(s∈S)$，采取动作$a(a∈A)$动作能够获得收益的期望，环境会根据动作反馈相应的回报reward r，所以算法的主要思想就是将State与Action构建成一张Q-table来存储Q值，然后根据Q值来选取能够获得最大的收益的动作。

- **2、公式推导**

    目标是求出累计奖励最大的策略的期望：
    $$
    \text { Goal: } \max _{\pi} E\left[\sum_{t=0}^{H} \gamma^{t} R\left(S_{t}, A_{t}, S_{t+1}\right) \mid \pi\right]
    $$
    （$S$：状态集合，$A$：动作集合，$R$：回报函数，$H$：范围，$\gamma$：折损系数，$\pi$：在$S_{t}$状态下采取动作$A_{t}$策略 ）

    时间差分法TD（融合了蒙特卡洛和动态规划）进行离线学习，使用bellman方程对马尔科夫过程求解最优策略，（此处有点模糊，待补充），更新公式如下：
    $$
    Q(s, a) \leftarrow Q(s, a)+\alpha\left[r+\gamma \max_{a^{\prime}} Q\left(s^{\prime}, a^{\prime}\right)-Q(s, a)\right]
    $$
    （$\alpha$：学习率，$\gamma$：奖励性衰变系数，$r$：回报值，$Q$：Q-table值）
    （$新Q(s, a) = 老Q(s, a) + \alpha * (Q现实 - Q估计)$）
    （根据下一个状态$s^{\prime}$中选取最大的$Q\left(s^{\prime}, a^{\prime}\right)$值乘以衰变$\gamma$加上真实回报值$r$作为Q现实，之前Q表里面的$Q(s,a)$作为Q估计）

## 2. **Sarsa**

- **1、算法思想**

    Sarsa与Q-Learning一样，在更新table时有区别，它属于On-policy，训练过程中会比较保守，其实就是怂，Q-Learning是苏维埃，Sarsa就是美利坚。

    相对于Sarsa来说，它的变种Sarsa($\lambda$)中多了一个矩阵E (eligibility trace)，用来保存在路径中所经历的每一步，因此在每次更新时也会对之前经历的步进行更新。

    参数$\lambda$取值范围为$[0, 1]$，如果$\lambda = 0$，Sarsa($\lambda$) 将退化为Sarsa，即只更新获取到 reward 前经历的最后一步；如果 $\lambda = 1$，Sarsa($\lambda$) 更新的是获取到 reward 前的所有步。$\lambda$可理解为脚步的衰变值，即离最终目标越近的步越重要。

## 3. **Deep Q Network**

- **1、算法思想**

    Q-Leanring在状态和动作空间是高维连续时，使用Q-table表示动作空间和状态就很困难。

    DQN用神经网络来拟合一个函数代替Q-table，使用一个eval_net产生Q估计（当前网络），使用另外一个target_net产生Q现实（目标网络），同时它设置了一个experience replay（经验池）用于存放之前的动作和状态，在学习过程中随机的加入之前的经验让神经网络更有效率。

    ![DQN](https://wx1.sinaimg.cn/mw2000/0069EgM5gy1grzlnqtoerj30og0hdjt2.jpg)

    Double DQN和Nature DQN结构一样，在Nature DQN的基础上，先在当前Q网络中先找出最大Q值对应的动作，再把这个动作放到目标网络里面去计算目标Q值，来消除过度估计的问题。

    Prioritized Experience Replay (DQN)，字面意思就可以理解，就是把样本做一个优先级处理，一般SumTree树形结构排序，batch抽样的时候不是随机抽样，按照Memory中的样本优先级来抽，更有效地找到需要学习的样本。

    Dueling DQN把特征层和输出层之间的全连接层分成了两部分，一部分用于近似state-value V(s)，另一部分近似Advantage-Function A(s, a)，求和得到最终的Q(s, a)。

    ![Dueling DQN](https://wx1.sinaimg.cn/mw2000/0069EgM5gy1grzlnvfcpcj31dg0we0zy.jpg)

## 4. **Policy Gradients**

- **1、算法思想**

    Policy Gradients可以在一个连续区间输出动作，利用reward值来引导某一个动作是否应该增加被选的概率（基于概率的算法），通过更新神经网络来决定输出策略，根据每一回合的输赢来判断该回合中的每一步是好还是坏（回合更新）。

## 5. **Actor Critic**

- **1、算法思想**

    从 Critic （单步更新）评判模块得到对动作的好坏评价，然后反馈给 Actor，也就是乘以Actor的输出值（策略概率），让 Actor 更新自己的策略。这两个模块用不同的神经网络来代替。Actor Critic的不足之处在于：每次都是在连续状态中更新参数，每次参数更新前后都存在相关性，导致神经网络只能片面的看待问题，甚至导致神经网络学不到东西。

## 6. **Deep Deterministic Policy Gradient（DDPG）**

- **1、算法思想**

    Deep Deterministic Policy Gradient可拆解为两大部分：Deep和Deterministic Policy Gradient。Deep指的是DQN算法中使用一个记忆库和两个结构相同但参数不同的神经网络。Deterministic Policy Gradient指：Policy Gradient本是随机选择一个分布的动作，但现在Deterministic Policy Gradient改变了输出动作过程，使得输出更加确定。

## 7. **Asynchronous Advantage Actor-Critic（A3C）**

- **1、算法思想**

    创建多个并行环境，让多个拥有副结构的agent同时在这些并行环境上更新主环境中的参数（效率高）。并行的agent互不干扰，且主环境的更新受到副结构更新的不连续干扰（相关性降低，收敛性提高）。类似于有多个相同的机器人共同干着一件事，比如玩游戏，他们的经验上传到中央大脑，又从中央大脑获取到玩游戏的方法。

## 8. **Proximal Policy Optimization（PPO）**

- **1、算法思想**

    PPO主要是限制更新步长，因为学习速率慢时间就长，学习速率快就容易躁动。限制的部分就在于actor的更新，A会乘一个新旧概率比，如果差距大优势大那么学习幅度就加大，同时还要减去一个新旧的分布的KL，做以惩罚项，让他们不要差距太大。另外也可以不要KL的惩罚项，因为惩罚也是为了限制它的更新速度，那么直接对新旧比的出现幅度进行控制就可以了。（比如倍数不超过多少倍），即clip。

    Distributed PPO就是类似与AC3的多线程。
