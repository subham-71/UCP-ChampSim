# UCP-ChampSim CS204 Project

# Group Members:

Aman Kumar                    2020CSB1153
Aman Pankaj Adatia            2020CSB1154
Ojassvi Kumar                 2020CSB1187
Rishabh Jain                  2020CSB1198
Subham Subhasis Sahoo         2020CSB1317


# Execution steps:

For 2 core:
./build_champsim.sh bimodal no no no no lru 2
./run_2core.sh bimodal-no-no-no-no-lru-2core 1 10 0 gcc_13B.trace.xz gcc_13B.trace.xz


For 4 core:
./build_champsim.sh bimodal no no no no lru 4 
./run_4core.sh bimodal-no-no-no-no-lru-4core 1 10 0 gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz


For 8 core:
./build_champsim.sh bimodal no no no no lru 8
./run_8core.sh bimodal-no-no-no-no-lru-8core 1 10 0 gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz gcc_13B.trace.xz


Note: Traces can be changed as per requirement.


# Code Explanation:

We have maintained the least recently used (LRU) replacement policy for each partition assigned to the core (cpu). If the partition size needs to be increased for a particular core say C1, i.e. the new partition size is greater than the current partition, then the algorithm will check for availability in some other core say C2 whose new partition size is less than the current partition assigned to it. So the last recently accessed way from C2 will be assigned to C1 and accordingly allotted as MRU at that stage. 
We have created Auxiliary Tag Directory (ATD) to maintain in the form of UMON-global. We have maintained a vector to count the number of hits for each way, and also a variable to maintain total accesses. Our repartition algorithm follows the greedy approach based on the Utility-based Cache Partitioning research paper.


# Results:

For 4 core:
Trace: gcc_13B in all cores
Case 1: No repartitioning (Static partitioning)
        IPC for core 0: 0.107372
        IPC for core 1: 0.107081
        IPC for core 2: 0.107522
        IPC for core 3: 0.106291

Case 2: Repartitioning after 1000 accesses (Dynamic partitioning)
        IPC for core 0: 0.112475
        IPC for core 1: 0.114162
        IPC for core 2: 0.116858
        IPC for core 3: 0.113123


Core 0 : 
        No repartitioning (Static partitioning)
        LLC TOTAL     ACCESS:     114677  HIT:      17480  MISS:      97197
        
        Repartitioning after 1000 accesses (Dynamic partitioning)
        LLC TOTAL     ACCESS:     113362  HIT:      41663  MISS:      71699
Core 1 :
        No repartitioning (Static partitioning)
        LLC TOTAL     ACCESS:     115009  HIT:      17432  MISS:      97577
        
        Repartitioning after 1000 accesses (Dynamic partitioning)
        LLC TOTAL     ACCESS:     114161  HIT:      42396  MISS:      71765
Core 2 :
        No repartitioning (Static partitioning)
        LLC TOTAL     ACCESS:     114676  HIT:      17658  MISS:      97018
        
        Repartitioning after 1000 accesses (Dynamic partitioning)
        LLC TOTAL     ACCESS:     115968  HIT:      43882  MISS:      72086
Core 3 :
        No repartitioning (Static partitioning)
        LLC TOTAL     ACCESS:     113887  HIT:      17023  MISS:      96864
        
        Repartitioning after 1000 accesses (Dynamic partitioning)
        LLC TOTAL     ACCESS:     114070  HIT:      39762  MISS:      74308


As we can see by using the repartitioning technique we have improved the Instructions per Cycle (IPC) count. We also observed a similar pattern when we executed the same code with 2 or 8 cores and different traces as can be observed from the results section.


Major changes have been done in the following files:
lru.llc_repl - LRU replacement policy for each core
main.cc - Repartitioning algorithm