

## 利用Gym实践

### 查看Gym中所有的环境

```python
# 查看Gym库的所有环境
from gym import envs
envs_space = envs.registry.all()
envs_ids = [env_spac.id for env_spac in envs_space]
print(envs_ids)
```



### 实践示例

```python
import gym  # 导包
env = gym.make("CartPole-v0")  # 构建实践环境
observation = env.reset()  # 重置环境
for _ in range(1000):
    env.render()  # 可视化
    action = env.action_space.sample()  # 从动作空间中随机选取一个动作
    observation, reward, done, info = env.step(action)  # 提交动作 并从环境得到反馈
    print(observation)
env.close()
```



### MountainCar-v0

```python
import gym  # 导包

env = gym.make("MountainCar-v0")
print('动作空间 = {}'.format(env.action_space))
print('观测空间 = {}'.format(env.observation_space))
print('观测范围 = {} - {}'.format(env.observation_space.low, env.observation_space.high))
print('动作数 = {}'.format(env.action_space.n))
'''
动作空间 = Discrete(3)
观测空间 = Box([-1.2  -0.07], [0.6  0.07], (2,), float32)
观测范围 = [-1.2  -0.07] - [0.6  0.07]
动作数 = 3
'''

def play_montecarlo(env, agent, render=False, train=False):
    episode_reward = 0.  # 记录回合总奖励，初始化为0
    observation = env.reset()  # 重置游戏环境，开始新回合
    while True:  # 不断循环，直到回合结束
        if render:  # 判断是否显示
            env.render()  # 显示图形界面，图形界面可以用 env.close() 语句关闭
        action = agent.decide(observation)
        next_observation, reward, done, _ = env.step(action)  # 执行动作
        episode_reward += reward  # 收集回合奖励
        if train:  # 判断是否训练智能体
            agent.learn(observation, action, reward, done)  # 学习
        if done:  # 回合结束，跳出循环
            break
        observation = next_observation
    return episode_reward  # 返回回合总奖励

class Agent:
    def __init__(self,env):
        pass

    def decide(self,observation):
        position,velocity = observation
        lb = min(-0.09 * (position + 0.25) ** 2 + 0.03,
                 0.3 * (position + 0.9) ** 4 - 0.008)
        ub = -0.07 * (position + 0.38) ** 2 + 0.07
        if lb < velocity < ub:
            action = 2
        else:
            action = 0
        return action  # 返回动作

    def learn(self,*args):
        pass

agent = Agent(env)
env.seed(0)  # 设置随机数种子,只是为了让结果可以精确复现
episode_reward = play_montecarlo(env,agent,render=True)
print('回合奖励 = {}'.format(episode_reward))
env.close()
```



### conclusion

使用env=gym.make(环境名)取出环境，使用 env.reset()初始化环境，使用env.step(动作)执行一步环境，使用 env.render()显示环境，使用env.close()关闭环境。

## 马尔科夫过程

马尔科夫性质

马尔科夫链

## 表格型方法



### 蒙特卡洛方法

是基于采样的方法，给定策略π，智能体与环境进行交互，得到许多轨迹，每一条轨迹都有对应的回报：

![image-20230228144254481](./pic\image-20230228144254481.png)

根据大数定律，只要我们得到足够多的轨迹，对所有轨迹的回报求平均值，也就趋近于某一个策略所对应状态的价值。

增量式蒙特卡洛方法，也就是通过采集数据，得到一个新的轨迹，对于整个轨迹采用增量的方法进行更新：

![image-20230228145336408](./pic\image-20230228145336408.png)

### 时序差分TD

时序差分是介于蒙特卡洛和动态规划之间的方法，它是免模型的，不需要马尔可夫决策过程的转移矩阵和奖励函数。此外，时序差分方法可以从不完整的回合中学习，并且结合了自举的思想。

时序差分的目的就是给定π，在线地计算价值函数V。

一步时序差分（TD(0)）即每往前走一步就做一步自举，用**估计回报**来更新上一时刻的价值函数V：
                          ![image-20230228144852942](D:\lifeng\md文档\pic\image-20230228144852942.png)

这个估计回报就是时序差分目标TD target，也就是带衰减的未来奖励总和，这个TD target使用的是之前的估计，

在蒙特卡洛方法里面，G是实际得到的值（可以看成目标），因为它已经把一条轨迹跑完了，可以算出每个状态实际的回报。时序差分不等轨迹结束，往前走一步，就可以更新价值函数。时序差分方法只执行一步，状态的值就更新。蒙特卡洛方法全部执行完之后，到了终止状态之后，再更新它的值。

时序差分方法还可以进一步推广，利用TD(2)，也就是往前走两步得到回报：

![image-20230228145800850](./pic\image-20230228145800850.png)

n趋于无穷大时，时序差分就变成了蒙特卡罗方法。



自举是指更新时使用了估计。蒙特卡洛方法没有使用自举，因为它根据实际的回报进行更新。动态规 划方法和时序差分方法使用了自举。



### Sarsa

也就是将原本时序差分更新V的过程变成了更新Q

![image-20230228150345501](./pic\image-20230228150345501.png)

Sarsa是一种同策略（on-policy）算法，它优化的是它实际执行的策略，它直接用下一步会执行的动作去优化Q表格。

### Q学习

Q表格：
其表示形式是表格，其中表格的横轴为动作（智能体的动作），纵轴为环境的状态，每一个坐标点对应某时刻智能体和环境的状态，并通过对应的奖励反馈选择被执行的动作。一般情况下，Q表格是一个已经训练好的表格，不过我们也可以每执行一步，就对Q表格进行更新，然后用下一个状态的Q值来更新当前状态的Q值（即时序差分方法）



Q学习是一种异策略（off-policy）算法。异策略在学习的过程中，有两种不同的策略：目标策略（target policy）和行为策略（behavior policy）。目标策略是我们需要去学习的策略，一般用π来表示,目标策略是不需要和环境进行交互的。

行为策略是探索环境的策略，一般用µ来表示。行为策略可以大胆地去探索到所有可能的轨迹，采集轨迹，采集数据，然后把采集到的数据“喂”给目标策略学习。

在异策略学习的过程中，轨迹都是行为策略与环境交互产生的，产生这些轨迹后，我们使用这些轨迹来更新目标策略π。

目标策略π直接在Q表格上使用贪心策略：

​									![image-20230228150934634](./pic\image-20230228150934634.png)

行为策略是一个随机的策略，基于Q表格逐渐改进。

Q学习目标的下一个动作都是通过argmax构造出来的：

![image-20230228151243913](./pic\image-20230228151243913.png)

Q学习的增量学习形式：
                    ![image-20230228151308507](./pic\image-20230228151308507.png)		



**关于Q-learning的code在Project: rl-tutorials-master/basis/Q_learning中。**

*Config类方便配置参数的方法很好*



以CliffWalking-v0为例，对于强化学习算法中的训练部分，每部分都需要重置环境，每个回合agent首先需要根据起始环境随机采样一个动作（随机采样动作使用的是e-greedy 策略），执行动作，得到一个transition(next_state, reward, terminated, info)，也就是与环境完成一次交互，然后agent更新学习的算法（此处主要是更新Q值），然后更新state，即state = next_state，接着可以记录一下本回合的reward，loss等。强化学习测试的代码跟训练基本上是一样的，相比于训练代码，测试代码主要有以下几点不同：1、测试模型的过程是不需要更新的；2、测试代码不需要采样动作，相比之代替的是预测动作，采样动作可能会采用各种策略例如e-greedy策略，而预测动作不需要，只需要根据训练时学习好的Q表或者网络模型代入状态得到动作即可；





### 强化学习的基本范式

1. 初始化环境和智能体
2. 对于每个训练回合，智能体首先选择动作，与环境交互得到反馈即下一个状态以及奖励
3. 智能体进行策略更新
4. 多个回合后算法收敛，保存模型，画图等。





## 策略梯度

## 近端策略优化PPO

如果要学习的智能体和与环境交互的智能体是相同的，我们称之为同策略。如果要学习的智能体和与环境交互的智能体不是相同的，我们称之为异策略。

**重要性采样**：假设有一函数f(x),计算从分布p采样x，再把x带入f，得到f(x)。若要计算f(x)的期望值，如果不方便对p做积分，可以从p中采样一些数据，将这些数据都带入到f中，并取平均值，这就可以近似得到f(x)的期望值。若现在不能从p中采样数据，且只能从另一个分布q中采样数据x，q可以是任意分布，此时计算f(x)就不可以使用之前的办法，因为之前的办法x是从p中采样的，使用如下办法：
                      ![image-20230301154649096](./pic\image-20230301154649096.png)

所以就算我们不能从p里面采样数据，但只要能从q里面采样数据，就可以计算从p采样x代入f以后的期望值。因为是从 q 采样数据，所以我们从 q 采样出来的每一笔数据，都需要乘一个重要性权重（importance weight） p(x)/q(x) 来修正这两个分布的差异。

PPO简单来说就是避免在使用重要性采样时由于不同的分布相差太多导致重要性采样结果偏差较大，具体来说就是在训练过程中加一个限制，对应两个分布输出动作的KL散度，也就是衡量两个采样分布的相似程度。

### PPO实现CarPole-v1（离散动作空间）      

关于CarPole-v1：

动作是离散的两个值：0-车向左移动；1-车向右移动

环境是四个部分：0：车的位置：[-2.4,2.4]；1：车速：[-inf,inf]；2：杆角度：[-41.8,41.8]；3：杆顶端的速度：[-inf,inf]

