太初平台设计介绍
==============

通用数据接口设计
--------
为了实现通用性，将数据流接口进行了固定的字典设计。由于多智能体强化学习环境的观测一般不止一项，因此将观测obs定义为一个字典，键如下定义：

.. code-block:: python
    obs={'agent_obs','last_action','global_state','action_mask','agent_id'}
与环境交互的transition数据定义为

.. code-block:: python
    time_step={'obs','action','next_obs','reward','done','prev_state','specific'}
为了防止混乱，所有的key均是单数，但是里面包含的数据可能是每个智能体的数据，如agent_obs里包含环境中所有agent的观测数据。time_step中'specific'键
的加入是为了支持一些算法特殊的数据记录需要，如mappo中的v的记录等。

传入网络进行计算的数据格式均为(T,B,A,N)的形式，T表示T步的连续交互，即T步的unroll，B表示Batch size，A表示智能体数量，N表示具体的数据形状，如果是图片则N=(C,H,W)，
这样的数据格式可以很方便的执行并行计算，如同构的共享权重模型，可以将数据形状转换为(T*B*A,N)的形式，将前三个维度视为Batch维度，一次性输入给网络进行推理。
对于异构模型也可以转换为(T*B,A,N)的形式取相应的智能体数据进行计算，免去了繁琐的索引切片操作。

每一步交互的数据被存入一个list，直到游戏结束，然后将这一整个episode的数据进行切片，切成T长度的小段，放入replay buffer中。

算法设计
--------


并行设计
--------




