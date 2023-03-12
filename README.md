
# Utility Based Cache Partioning - ChampSim

Utility-based cache partitioning is a technique used to partition a cache into multiple partitions to improve overall system performance or fairness. In this technique, the goal is to partition the cache in a way that maximizes the utility function, which measures the performance or fairness achieved by each partition.

The utility function is defined based on the application requirements, and it can be different for different applications. For example, for a system with multiple applications, the utility function can be defined as the overall system performance or fairness, while for a single application, the utility function can be defined as the application's performance.

The utility-based cache partitioning technique involves three steps:

1. **Profiling:** In this step, the cache access patterns of the application(s) are analyzed to determine the access frequency, size, and type of cache lines. This information is used to determine the cache requirements of the application(s).

2. **Partitioning:** Based on the profiling results, the cache is partitioned into multiple partitions to meet the cache requirements of each application. The partitioning is performed using a utility function that maximizes the performance or fairness achieved by each partition.

3. **Runtime Management:** During runtime, the cache partitions are dynamically adjusted based on the changing cache requirements of the application(s). This ensures that the cache is utilized optimally, and the overall system performance or fairness is maximized.

Utility-based cache partitioning has been used in many research studies to improve system performance or fairness in various computing environments, including multicore systems, virtualized systems, and cloud computing environments.

This project involves implementation of utility-based cache partitioning in an open-source cache simulator called ChampSim. ChampSim is developed by the University of Virginia. It is designed to simulate the behavior of a modern microarchitecture, including processor pipelines, caches, branch predictors, and memory systems.

ChampSim is highly configurable and provides support for a wide range of benchmarks and workloads. It supports several cache replacement policies, including LRU, MRU, and randomized replacement policies, as well as advanced replacement policies such as CAR (Conscious Adaptive Replacement) and SHiP (Set dueling and Hotness-aware Placement).

ChampSim also includes support for simulating different processor architectures, such as ARM and x86, and different cache organizations, such as unified caches and split caches.

ChampSim has been used in several research studies to evaluate the performance and energy efficiency of different cache designs and replacement policies. Its open-source nature allows researchers to modify and extend the simulator to support new features and use cases.
## Authors

- [@OptimalKnight](https://www.github.com/OptimalKnight)
- [@adatiaaman](https://www.github.com/adatiaaman)
- [@Amank2854](https://www.github.com/Amank2854)
- [@subham-71](https://www.github.com/subham-71)
- [@Ozbridge](https://www.github.com/Ozbridge)


## Run Locally

Clone the project

```bash
  git clone https://github.com/OptimalKnight/UCP-ChampSim.git
```

Go to the project directory

```bash
  cd UCP-ChampSim
```

Create and place the required traces in a 'trace' directory (a sample trace named 'gcc_13B.trace.xz' is being used here) and execute the following commands,

**For 2 core:**

    ./build_champsim.sh bimodal no no no no lru 2
    ./run_2core.sh bimodal-no-no-no-no-lru-2core 1 10 0 gcc_13B.trace.xz gcc_13B.trace.xz

**For 4 core:** 

    ./build_champsim.sh bimodal no no no no lru 4
    ./run_4core.sh bimodal-no-no-no-no-lru-4core 1 10 0 gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz

**For 8 core:**

    ./build_champsim.sh bimodal no no no no lru 8
    ./run_8core.sh bimodal-no-no-no-no-lru-8core 1 10 0 gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz  gcc_13B.trace.xz gcc_13B.trace.xz

Note: Traces can be changed as per the requirement.
## Working

To ensure that frequently accessed data stays in the cache, a least recently used (LRU) replacement policy is maintained for each partition. However, when the partition size needs to be increased for a specific core (C1) and there is not enough space available, the algorithm checks other cores (e.g., C2) whose new partition size is smaller than the current partition size assigned to it. In such cases, the least recently accessed way from C2 is assigned to C1 and marked as most recently used (MRU).

To maintain the cache partitioning information, an Auxiliary Tag Directory (ATD) is created in the form of UMON-global. The ATD contains a vector that counts the number of hits for each way and a variable that maintains total accesses. A repartitioning algorithm, based on the Utility-based Cache Partitioning research paper, is implemented using a greedy approach to ensure efficient allocation of cache space. This helps to maximize performance while minimizing contention for shared resources in multi-core systems.
## Observations (4 core)

Trace: gcc_13B in each core

**Case 1:** No repartitioning (Static partitioning) 

        IPC for core 0: 0.107372 
        IPC for core 1: 0.107081 
        IPC for core 2: 0.107522 
        IPC for core 3: 0.106291 

**Case 2:** Repartitioning per 1000 accesses (Dynamic partitioning) 

        IPC for core 0: 0.112475 
        IPC for core 1: 0.114162 
        IPC for core 2: 0.116858 
        IPC for core 3: 0.113123 

### Core-Wise Analysis

**Core 0 :**

        No repartitioning (Static partitioning)
        LLC TOTAL     ACCESS:     114677  HIT:      17480  MISS:      97197
        
        Repartitioning per 1000 accesses (Dynamic partitioning)
        LLC TOTAL     ACCESS:     113362  HIT:      41663  MISS:      71699
**Core 1 :**

        No repartitioning (Static partitioning)
        LLC TOTAL     ACCESS:     115009  HIT:      17432  MISS:      97577
        
        Repartitioning per 1000 accesses (Dynamic partitioning)
        LLC TOTAL     ACCESS:     114161  HIT:      42396  MISS:      71765
**Core 2 :**

        No repartitioning (Static partitioning)
        LLC TOTAL     ACCESS:     114676  HIT:      17658  MISS:      97018
        
        Repartitioning per 1000 accesses (Dynamic partitioning)
        LLC TOTAL     ACCESS:     115968  HIT:      43882  MISS:      72086
**Core 3 :**

        No repartitioning (Static partitioning)
        LLC TOTAL     ACCESS:     113887  HIT:      17023  MISS:      96864
        
        Repartitioning per 1000 accesses (Dynamic partitioning)
        LLC TOTAL     ACCESS:     114070  HIT:      39762  MISS:      74308


The results obtained from our study clearly indicate that using the repartitioning technique has led to a significant improvement in the Instructions per Cycle (IPC) count. This improvement was consistent across different test cases where we executed the code using 2 or 8 cores with varying traces. 
 

## Implemented/Modified Sections

Sections which were mainly implemented/modified,

- **replacement/lru.llc_repl:** LRU replacement policy for each core (FUCNTIONS: llc_initialize_replacement, llc_find_victim, llc_update_replacement_state).
- **src/main.cc:** Repartitioning algorithm (LINES: 506-542 & 903-904).
- **src/cache.cc:** Declaration and initialisation of ATD and increasing the counter based on read and write hits in LLC  (LINES: 4-6, 247 & 546).