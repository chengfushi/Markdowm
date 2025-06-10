[toc]

# 第七章 MapReduce

# 1.概述

## 1.1分布式并行编程

| **对比维度**         | **传统并行计算框架**                          | **MapReduce**                              |
|----------------------|---------------------------------------------|--------------------------------------------|
| **集群架构/容错性**  | 共享式（共享内存/存储），容错性差             | 非共享式，容错性好                          |
| **硬件/价格/扩展**   | 刀片服务器、高速网、SAN，价格贵，扩展性差      | 普通PC机，便宜，扩展性好                     |
| **编程/学习难度**    | what-how（需关注实现细节），难度高            | what（仅需关注逻辑），简单                   |
| **适用场景**         | 实时、细粒度计算、计算密集型任务              | 批处理、非实时、数据密集型任务               |

## 1.2MapReduce模型简介
MapReduce将复杂的、运行于大规模集群上的并行计算过程高度地抽象到了两个函数：Map和Reduce。
这样做有什么好处呢？
1. 编程容易，不需要掌握分布式并行编程细节，也可以很容易把自己的程序运行在分布式系统上，完成海量数据的计算
2. MapReduce采用“分而治之”策略，一个存储在分布式文件系统中的大规模数据集，会被切分成许多独立的分片（split），这些分片可以被多个Map任务并行处理
3. MapReduce设计的一个理念就是“计算向数据靠拢”，而不是“数据向计算靠拢”，因为，移动数据需要大量的网络传输开销
4. MapReduce框架采用了Master/Slave架构，包括一个Master和若干个SlaveMaster上运行JobTracker，Slave上运行TaskTracker
5. Hadoop框架是用Java实现的，但是，MapReduce应用程序则不一定要用Java来写。
## 1.3Map和 Reduce函数
| **函数**     | **输入示例**                               | **输出示例**                          | **说明**                                                                              |
| ---------- | -------------------------------------- | --------------------------------- | ----------------------------------------------------------------------------------- |
| **Map**    | `<行号, "a b c">`                        | `List(<"a",1>, <"b",1>, <"c",1>)` | 1. 将小数据集解析成一批 `<key,value>` 对，输入Map函数处理。<br>2. 每个输入的 `<k1,v1>` 会输出一批中间结果 `<k2,v2>`。 |
| **Reduce** | `<"a", <1,1,1,1>>`（即 `<k2, List(v2)>`） | `<"a", 3>`（即 `<k3,v3>`）           | 输入的 `<k2, List(v2)>` 中，`List(v2)` 是同一 `k2` 的 value 集合，Reduce 对它们进行合并计算。             |
# 2.MapReduce体系结构
MapReduce体系结构主要由四个部分组成，分别是：Client、JobTracker、TaskTracker以及Task。
```mermaid
graph TD
    subgraph Cluster
        A[Client]
        B[Client]
        C[Client]
        D[JobTracker]
        E[Task Scheduler]
        F[TaskTracker]
        G[TaskTracker]
        H[TaskTracker]
        I[Map Task]
        J[Map Task]
        K[Map Task]
        L[Map Task]
        M[Map Task]
        N[Map Task]
        O[Reduce Task]
        P[Reduce Task]
        Q[Reduce Task]
    end

    %% Client submissions
    A --> D
    B --> D
    C --> D

    %% Job scheduling
    D --> E
    E --> F
    E --> G
    E --> H

    %% Heartbeats (dashed lines)
    D -.-> F
    D -.-> G
    D -.-> H

    %% Task assignments
    F --> I
    F --> J
    F --> O
    G --> K
    G --> L
    G --> P
    H --> M
    H --> N
    H --> Q

    %% Styling (English-only compliant syntax)
    style Cluster fill:#f0f0f0,stroke:#333
    classDef default text-align:center
```

| 组件名称            | 核心功能描述              | 关键机制/特性                                                                               |
| --------------- | ------------------- | ------------------------------------------------------------------------------------- |
| **Client**      | 用户与MapReduce系统的交互接口 | - 提交MapReduce程序到JobTracker<br>- 提供作业状态查询接口                                            |
| **JobTracker**  | 集群资源管理与作业调度中心       | - 监控所有TaskTracker和作业健康状态<br>- 任务失败时自动转移<br>- 资源使用跟踪与调度器协同（通过TaskScheduler分配空闲资源）      |
| **TaskTracker** | 节点资源管理与任务执行代理       | - 周期性通过"心跳"汇报资源/任务状态<br>- 使用Slot划分资源（Map Slot/Reduce Slot）<br>- 执行JobTracker下发的任务启停指令 |
| **Task**        | 实际计算任务执行单元          | - **MapTask**：处理输入数据分片<br>- **ReduceTask**：执行聚合计算<br>- 均由TaskTracker动态启动              |

# 3.Map Reduce工作流程
## 3.1工作流程概述

```mermaid
graph LR
    %% 输入分片
    subgraph input_section["输入"]
        A0["分片0"]
        A1["分片1"]
        A2["分片2"]
        A3["分片3"]
        A4["分片4"]
    end

    %% Map阶段
    subgraph map_section["Map任务"]
        B0["map()"]
        B1["map()"]
        B2["map()"]
        B3["map()"]
        B4["map()"]
    end

    %% Shuffle节点
    C["Shuffle"]

    %% Reduce阶段
    subgraph reduce_section["Reduce任务"]
        D0["reduce()"]
        D1["reduce()"]
        D2["reduce()"]
        D3["reduce()"]
    end

    %% 输出结果
    subgraph output_section["输出"]
        E0["输出0"]
        E1["输出1"]
        E2["输出2"]
    end

    %% 连接关系（严格换行）
    A0 --> B0
    A1 --> B1
    A2 --> B2
    A3 --> B3
    A4 --> B4

    B0 --> C
    B1 --> C
    B2 --> C
    B3 --> C
    B4 --> C

    C --> D0
    C --> D1
    C --> D2
    C --> D3

    D0 --> E0
    D1 --> E1
    D2 --> E2
    D3 --> E2

    %% 样式定义（严格分号结尾）
    classDef inputStyle fill:#fff,stroke:#333;
    classDef mapStyle fill:#fff,stroke:#333,shape:ellipse;
    classDef reduceStyle fill:#fff,stroke:#333,shape:ellipse;
    classDef outputStyle fill:#fff,stroke:#333;
    classDef shuffleStyle fill:#fff,stroke:#333,stroke-dasharray:3;

    %% 样式应用（每行独立且分号结尾）
    class input_section inputStyle;
    class map_section mapStyle;
    class reduce_section reduceStyle;
    class output_section outputStyle;
    class C shuffleStyle;
```
* 不同的Map任务之间不会进行通信
* 不同的Reduce任务之间也不会发生任何信息交换
* 用户不能显式地从一台机器向另一台机器发送消息
* 所有的数据交换都是通过MapReduce框架自身去实现的
## 3.2MapReduce各个执行阶段
```mermaid
graph TD
    subgraph 节点1["节点1"]
        A["从分布式文件系统中加载文件"] --> B["InputFormat"]
        B --> C["Split"]
        C --> D["RR"]
        D --> E["Map"]
        E --> F["Shuffle"]
    end

    subgraph 节点2["节点2"]
        G["从分布式文件系统中加载文件"] --> H["InputFormat"]
        H --> I["Split"]
        I --> J["RR"]
        J --> K["Map"]
        K --> L["Shuffle"]
    end

    F --> M["Reduce"]
    L --> M

    %% 样式定义
    style 节点1 fill:#f0f0f0,stroke:#333
    style 节点2 fill:#f0f0f0,stroke:#333
    style A fill:#e0e0e0,stroke:#666
    style B fill:#d3d3d3,stroke:#555
    style C fill:#d3ef8e,stroke:#4a7
    style D fill:#ffc0cb,stroke:#8b008b
    style E fill:#ffa07a,stroke:#ff4500
    style F fill:#dda0dd,stroke:#8b008b
    style G fill:#e0e0e0,stroke:#666
    style H fill:#d3d3d3,stroke:#555
    style I fill:#d3ef8e,stroke:#4a7
    style J fill:#ffc0cb,stroke:#8b008b
    style K fill:#ffa07a,stroke:#ff4500
    style L fill:#dda0dd,stroke:#8b008b
    style M fill:#dda0dd,stroke:#8b008b
```

### 3.2.1关于Split(分片)
```mermaid
graph TD
    %% 文件声明
    F["A file on HDFS"] --> B1["block 1"]
    F --> B2["block 2"]
    F --> B3["block 3"]
    F --> B4["block 4"]
    F --> B5["block 5"]
    F --> B6["block 6"]

    %% Split关系
    B1 --> S1["split 1"]
    B2 --> S2["split 2"]
    B3 --> S3["split 3"]
    B4 --> S4["split 4"]
    B5 --> S3
    B6 --> S4

    %% DataNode节点
    subgraph DataNodes["DataNodes"]
        DN1["DataNode1"]
        DN2["DataNode2"]
        DN3["DataNode3"]
        DN4["DataNode4"]
        DN5["DataNode5"]
        DN6["DataNode6"]
    end

    %% 数据块分布（虚线箭头）
    B1 -.-> DN1
    B1 -.-> DN2
    B1 -.-> DN3
    B2 -.-> DN1
    B2 -.-> DN2
    B2 -.-> DN4
    B3 -.-> DN3
    B3 -.-> DN5
    B3 -.-> DN6
    B4 -.-> DN4
    B4 -.-> DN5
    B4 -.-> DN6
    B5 -.-> DN1
    B5 -.-> DN3
    B5 -.-> DN5
    B6 -.-> DN2
    B6 -.-> DN4
    B6 -.-> DN6

    %% 样式定义（严格分号结尾）
    classDef file fill:#f5f5f5,stroke:#333,stroke-width:2px;
    classDef block fill:#e3f2fd,stroke:#2196f3;
    classDef split fill:#bbdefb,stroke:#0d47a1;
    classDef datanode fill:#e8f5e9,stroke:#4caf50;
    linkStyle 6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23 stroke:#795548,stroke-dasharray:3;

    %% 样式应用（每行独立且分号结尾）
    class F file;
    class B1,B2,B3,B4,B5,B6 block;
    class S1,S2,S3,S4 split;
    class DN1,DN2,DN3,DN4,DN5,DN6 datanode;
```
HDFS以固定大小的block为基本单位存储数据，而对于MapReduce而言，其处理单位是split。split是一个逻辑概念，它只包含一些元数据信息，比如数据起始位置、数据长度、数据所在节点等。它的划分方法完全由用户自己决定。
### 3.2.2Map任务的数量
Hadoop为每个split创建一个Map任务，split的多少决定了Map任务的数目。大多数情况下，理想的分片大小是一个HDFS块.
```mermaid
graph LR
    %% 文件主体
    F["A file on HDFS"] --> B1["block 1"]
    F --> B2["block 2"]
    F --> B3["block 3"]
    F --> B4["block 4"]
    F --> B5["block 5"]
    F --> B6["block 6"]

    %% Split分割线（虚线）
    B1 -.-> S1["split 1"]
    B2 -.-> S2["split 2"]
    B3 -.-> S3["split 3"]
    B4 -.-> S4["split 4"]
    B5 -.-> S3
    B6 -.-> S4

    %% 样式定义
    classDef file fill:#fff,stroke:#333,stroke-width:2px;
    classDef block fill:#f0f0f0,stroke:#666;
    classDef split fill:#fff,stroke:#999,stroke-dasharray:3;

    %% 样式应用
    class F file;
    class B1,B2,B3,B4,B5,B6 block;
    class S1,S2,S3,S4 split;
```
### 3.2.3Reduce任务的数量
* 最优的Reduce任务个数取决于集群中可用的reduce任务槽(slot)的数目
* 通常设置比reduce任务槽数目稍微小一些的Reduce任务个数（这样可以预留一些系统资源处理可能发生的错误）
## 3.3Shuffle过程详解
### 3.3.1Shuffle过程简介
```mermaid
graph LR
    %% 输入部分
    Input["输入"] --> Map

    %% Map任务部分
    subgraph Map任务
        Map["Map"] --> Buffer["缓存"]
        Buffer --> Spill["溢写\n(分区、排序、合并)"]
        Spill --> Partitions["多个分区"]
    end

    %% Reduce任务部分
    subgraph Reduce任务
        Partitions --> Reduce["Reduce"]
        Reduce --> Merge1["磁盘文件归并"]
        Merge1 --> Merge2["归并"]
        Merge2 --> Output["输出"]
    end

    %% 其他任务连接
    Map -.->|"其他Map任务"| OtherMap
    Reduce -.->|"其他Reduce任务"| OtherReduce

    %% 样式定义
    classDef map fill:#d4e6ff,stroke:#1a73e8;
    classDef reduce fill:#fce8e6,stroke:#d93025;
    classDef process fill:#e6f4ea,stroke:#34a853;
    classDef inputOutput fill:#f8f9fa,stroke:#5f6368;

    %% 样式应用
    class Map,Spill,Partitions map;
    class Reduce,Merge1,Merge2 reduce;
    class Buffer,Input,Output inputOutput;
    class OtherMap,OtherReduce process;

    %% 箭头样式
    linkStyle 0,1,2,3,4,5 stroke:#34a853,stroke-width:2px;
    linkStyle 6,7 stroke:#5f6368,stroke-dasharray:3;
```

### 3.3.2Map端Shuffle过程
```mermaid
%%{init: {'themeVariables': {'primaryColor': '#fff'}}}%%
graph TD
    classDef style1 fill:#2B7A78,color:white,stroke:#17252A
    classDef style2 fill:#3AAFA9,color:white,stroke:#17252A
    classDef dashed stroke-dasharray:5 5

    subgraph 流程步骤
    step1["① 输入数据和执行Map任务"] --> step2["② 写入缓存"]
    step2 --> step3["③ 溢写（分区、排序、合并）"]
    step3 --> step4["④ 文件归并"]
    end

    step1 --> Map任务[[Map任务]]
    step2 --> 缓存[("缓存")]
    step3 --> 溢写操作[/"分区、排序、合并"/]
    step4 --> 结果[["最终文件"]]

    subgraph 操作区域
        cacheblock[ ]
        spillblock[ ]
        缓存 & 溢写操作
    end

    class step1,step2,step3,step4 step
    class Map任务,缓存,结果 style1
    class 溢写操作 style2
    class cacheblock,spillblock dashed
    classDef step fill:none,stroke:none

    linkStyle 0,1,2,3 stroke:#17252A,stroke-width:2px
```
1. 每个Map任务分配一个缓存MapReduce默认100MB缓存
2. 设置溢写比例0.8、分区默认采用哈希函数排序是默认的操作
3. 排序后可以合并（Combine）
4. 合并不能改变最终结果
5. 在Map任务全部结束之前进行归并
6. 归并得到一个大的文件，放在本地磁盘
7. 文件归并时，如果溢写文件数量大于预定值（默认是3）则可以再次启动Combiner，少于3不需要
8. JobTracker会一直监测Map任务的执行，并通知Reduce任务来领取数据

### 3.3.3Reduce端Shuffle过程
```mermaid
%%{init: {'theme':'base','themeVariables':{'primaryColor':'#fff'}}}%%
graph TD
    classDef map fill:#B3E5FC,stroke:#03A9F4
    classDef reduce fill:#FFE0B2,stroke:#FF9800
    classDef storage fill:#C8E6C9,stroke:#4CAF50
    classDef merge fill:#F8BBD0,stroke:#E91E63
    classDef dashed stroke-dasharray:5 5

    subgraph Map区域["▣ Map任务区"]
        map1[["Map任务"]] --> disk1[("磁盘")]
        disk1 --> partition1[分区]
        disk1 --> partition2[分区]
        disk1 -.-> otherMap[其他Map任务]
    end

    subgraph Reduce区域["▣ Reduce任务区"]
        receive["1.领取数据"] --> cache[("缓存")]
        cache --> merge2["2.归并数据"]
        merge2 --> fileMerge[文件归并]
        fileMerge --> reduceTask[["Reduce任务"]]
        reduceTask -.-> otherReduce[其他Reduce任务]
    end

    partition1 -. 数据流 .-> receive
    partition2 -. 数据流 .-> receive
    fileMerge --> reduceTask
    merge2 --> fileMerge

    class map1,otherMap map
    class receive,cache,merge2,fileMerge,reduceTask,otherReduce reduce
    class disk1,partition1,partition2 storage
    class fileMerge merge
    class otherMap,otherReduce dashed

    linkStyle 0,1,2,3 stroke:#03A9F4,stroke-width:1.5
    linkStyle 4,5,6,7 stroke:#FF9800,stroke-width:1.5
    linkStyle 8,9,10,11 stroke:#E91E63,stroke-width:1.5
```

* Reduce任务通过RPC向JobTracker询问Map任务是否已经完成，若完成，则领取数据
* Reduce领取数据先放入缓存，来自不同Map机器，先归并，再合并，写入磁盘
* 多个溢写文件归并成一个或多个大文件，文件中的键值对是排序的
* 当数据很少时，不需要溢写到磁盘，直接在缓存中归并，然后输出给Reduce
