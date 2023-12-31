## CS61C Notes

---
### CHAPTER IV The Processor



#### LEC 14 Introduction to Synchronous Digital Systems

- Synchronous: all operations coordinated by a central clock
- Transistor: By using n-channel and p-channel(controlled by volt), we can build **a NAND gate** 
  - Chips are composed of nothing but transistors and wires.
- Adder propagation delay
- SDS are made uup of two types of circuits:
  - Combinational logic circuits: a function of the inputs only
  - State Elements: store information



#### LEC 15 State

- Some block need a state element, which can
  - store values for some indeterminate amount of time
  - help control the flow of information between combinational logic blocks
- Flip-flop
  - Register is **n instances of a "Flip-Flop"**
  - Rising edge-triggered
  - setup time / hold time / clk-to-q delay
- Finite State Machines



#### LEC 16 Combinational Logic

- Trutu Tables
- Logic Gate: Know the representation of each gate
- Boolean Algebra 
  - help us simplify the circuit
  - canonical form help us convert a truth table to a logical expression 



#### LEC 17 Combinational Logic Blocks

- Data Multiplexors
  - List the truth table -> Write the logical expression -> Draw the gate diagram
  - Hierarchically: 1 4-to-1 multiplexor -> 3 2-to-1 multiplexors
- ALU (Arithmetic Logic Unit)
  - Use multiplexors to control the behavior.
- Adder 
  - `si = XOR(ai, bi, ci)`
  - `ci+1 = MAJ(ai, bi, ci) = aibi + aici + bici`
  - `overflow = cn XOR cn-1`
  - Subtractor: `A - B = A + (-B)`
- N-bit adder-subtractor done using N 1-bit adders with XOR gates on input
- This is magic. Add op is indeed inplemented by XOR op.



#### LEC 18-20 Single-Cycle CPU Datapath

READ P&H 4.1 4.3 4.4

- Know the simple design a single-cycle cpu datapath.



#### LEC 21-23 Pipelining

READ P&H 4.5 4.6 4.7 4.8 4.10

- 直接看教材
- 4.5: 为了加速使用了流水线， 可以看到使用了流水线之后，每个 clock cycle 基本上四个 component 都在工作。除此之外还简要介绍了三个 hazard 以及应对策略
  - structual hazard: multiple instructions use a component simultaneously
  - data hazard: an instruction uses the data in the previous instruction
    - 归根结底还是流水线导致前一条指令还没完成，这一条指令已经开始了
    - Forwarding 应对
    - 经典的 lw-delay，通过 reorder 应对
  - control hazard: Before the ALU stage, can't decide next instruction.  
    - branch-prediction
    - 最直观的理解了分支预测，以前尽管知道有分支预测，但一直不知道为什么要分支预测
- 4.6: 为此需要更改 datapath, 在每个步骤间添加了一个寄存器来存储中间状态，还更改了控制信号逻辑
- 4.7: 正式介绍 data hazard 的 data path
  - ![](Ref/1.png)
  - 对于一个 ALU，它的 input 需要从 register file 的数据和 EX/MEM 或 MEM/WB 寄存器中选择，所以我们再次需要 multiplexor
  - 对于一个 load 后接一个 R 类操作，forward 也解决不了，此时只能 stall。所以我们额外还需要一个 hazard detection unit 来及时停止某些指令
- 4.8: 正式介绍 control hazard 的 data path
  - 为了减小预测错误的影响，将分支地址计算提前
  - 这需要我们改进设计，添加 forward 逻辑和 stall 逻辑
  - 动态分支预测
    - branch prediction buffer
    - 一些简单的策略

- 4.10: 探究更复杂的 **instruction-level parallelism**
  - increase the depth of the pipeline: 即拆解更多的步骤使重叠更多
  - multiple issue: 多发射，即同时进行多次步骤。如同时 fetch 三条指令，使`CPI < 1`

---

### CHAPTER5 Large and Fast: Exploiting Memory Hierarchy



READ P&H 5.1 5.2 5.3 5.4 5.8 5.9 5.13

- 5.1 5.2: 描述了基本存储层次
  - 我们向上提供了一种抽象：`内存仿佛是无限可用的，但速度又是极快的`
  - 还介绍了基本的器件 SRAM(用于高速缓存) DRAM(用于内存)
- 5.3: 讲解了一个简单的 Cache Table，探讨了 Cache Miss 和 Write
  - 比如 Instruction Cache Miss，对于 out-of-order CPU，可以使其继续执行别的指令
  - Write 会有 Cache Consistency
    - write back 是比较常用的，在 CMU15445 Proj1 中便使用的是这种方式。通过 dirty 来 **lazy** write back
- 5.4 Mesuring and Improving Cache Performance
  - AMAT(Averge Memory Access Time) = Time for a hit + Miss rate * Miss penalty
  - Set-Associative Cache
    - 其 Miss rate 更小的代价是使用了更多的器件所带来的 delay，比如 comparator 的 delay（尽管 in parallel，但还是会有），multiplexor 的 delay。但这都比 penalty 要小的多了。

  - Multilevel Cache，现在电脑都是这样 L1, L2 甚至 L3。教材$\ p_{794}\ $给出了一个例子
  - 一个程序执行的性能与能否利用好缓存是挂钩的
- 5.8: A Common Framework for Memory Hierarchy
  - 略，似乎就是一个总结

- 5.9: Using a Finite-State Machine to Control a Simple Cache
  - 略，描述了一个简单的设计

- 5.13: 一个例子，可以大概看一下参数，比如各级缓存的大小，以及是 ?-way associative




#### LEC 24 Cache I

- 同 5.1 5.2

#### LEC 25

- 介绍了 directed-map cache 同 5.3 (更加容易理解)
- 这个 Cache 的最大缺点是非常容易 Cache Miss, 一个 Example 在教材$\ p_{786}$

#### LEC 26

- 介绍了 Fully Associative Cache 同 5.4 (更加容易理解)
- 其最大缺点是要扫描所有的 Cache 来寻找一个地址

#### LEC 27

- 介绍了 Set-Associative Cache 同 5.4 (更加容易理解)，这是一种调和的 Cache
- Block Replacement Strategy
- AMAT
- Windows 查看电脑缓存指令：`wmic cpu get L2CacheSize`
  - 我的电脑 L2 是 4096kb，L3 是 8192kb
- Linux `lscpu | grep "L2 cache"`



READ P&H 5.7

- 5.7: Virtual Memory
  - 两个 motivation
    - 不同的应用不能访问对方的地址空间
    - 物理内存有限，且使用需要耗费心力安排使之不重叠
  - 其使 Main Memory 为 Secondary Memory 的 Cache 
  - 单位是 page
  - **可以看到我们挑选两种层次的时候，我们使用的是不同的地址解释**。在高速缓存与内存间，我们使用 Block 为单位，不论是高速缓存还是内存都视为 Block 的集合。而此时，我们将 内存 与 磁盘都视为 page 的集合
    - 一个 64 位地址，归根结底，也只是 64 个 bit。但**我们可以根据不同的解释来形成不同的理解**。

---

#### LEC 28 OS

- 课程的科普性很强，当深陷于细节时，不妨听听课程。能使我们从宏观上有一个更清晰的了解
- 介绍了操作系统的很多概念，包括上下文切换、中断等等
- 因为之前早已学过，所以快速听了一遍，宏观上的认知更加清晰了。也只会花费十分钟这样子

#### LEC 29-30 Virtual Memory

- 太老生常谈了，参见 CSAPP 对应部分

#### LEC 31 IO

- 介绍了 IO 的基本概念，IO设备有一个统一的接口
- IO轮询和中断
- **DMA**: 将主存与磁盘间的数据转移交给别的硬件，CPU 去执行别的指令，DMA 执行完后中断来通知 CPU

---

#### LEC 32 Parllelism

- 介绍了几种模式，其中最常见的是 SIMD, MIMD
- 重点讲解了 SIMD，即一条指令多个 PU 来执行，属于 Data-Level Parallelism
- Intel 的 SIMD: MMX(MultiMedia Extension), SSE(Streaming SIMD Extension) 等等
- 这些额外的指令集越来越庞大，也要保存越来越多的状态
- 所以我们需要更多的 New and Wider Registers
- 在例子中可以看到，我们的每一个指令都操作更多的位数。比如 _mm_mul_pd(a, b)，a 和 b 都向量化了，每个都代表了两个值。所以这条指令的操作并行的使用了数据

#### LEC 33 Thread-Level Parallelism I

- 我们已经在这门课中学过的并行方式
  - Instruction-Level Parallelism: Pipeline
  - Parallel data: SIMD
  - Parallel Hardware: 32 bit AND GATE
- Multiprocessor Execution Model
  - Separate resources
    - Datapath(Register, PC, ALU)
    - L1, L2 Cache
  - Shared resources
    - Memory
    - L3 Cache
- 从大约 2005 年开始，单核的频率基本已经停步不前。人们转而开始增加 CPU 核数来提升性能
- Thread 就是一个指令流。通过 time sharing，似乎每个 Thread 都在同时执行
- 经典的 N-to-M 模型，N个软件线程对应M个核数的线程
- 超线程模拟出更多的 Logical CPUs

#### LEC 34 Thread-Level Parallelism II

- Open MP: C extension, offer us multi-threaded, shared-memory parallelism
- 几个例子讲解了 OpenMp 的使用

#### LEC 35 Thread-Level Parallelism III

- Cache consistency

- false sharing: 第四种类型的 Cache Miss

  - 两个 CPU 同时想获取在一个缓存行上的 data。两个只有一个会成功。

  - 课堂上提到可以使用更小的 cache block size 来解决

  - 还可以通过填充空字段使数据不在一个缓存行上

    - 但是一方面依赖提前知道硬件的信息，另一方面浪费了缓存。
