Team members: Hyunwoo Zong, Hyun Been Bae

CONTRIBUTIONS

Task 1
• Basic code structure and implementation - Hyunwoo Zong
• Code Review and modifications - Hyun Been Bae

Task 2
• Test run for task 1 and 3 - Hyunwoo Zong

Task 3
• Basic code structure and implementation - Hyunwoo Zong
• Code Review and modifications - Hyun Been Bae

Task 4
• Experiment & data collection, data analysis - Hyun Been Bae
• Brief analysis, conclusion, minor code updates - Hyunwoo Zong


REPORT

Threshold, Real Time (Elapsed), User Time, Sys Time
"2,097,152", 0.396s, 0.372s, 0.018s
"1,048,576", 0.238s, 0.381s, 0.039s
"524,288", 0.151s, 0.407s, 0.036s
"262,144", 0.140s, 0.438s, 0.061s
"131,072", 0.200s, 0.465s, 0.060s
"65,536", 0.130s, 0.470s, 0.093s
"32,768", 0.139s, 0.504s, 0.111s
"16,384", 0.123s, 0.530s, 0.154s

The data shows a clear trend where decreasing the parallel threshold generally leads to a reduction in real (wall-clock) time. This improvement occurs because a lower
threshold allows the quicksort algorithm to partition the work into more child processes earlier in the recursion, which creates more subproblems that are executed in
parallel. Since the data is mapped using mmap with MAP_SHARED, these child processes can work on independent segments of the array simultaneously across multiple CPU
cores, allowing the workload to be distributed.

As the threshold decreases, the amount of parallelism increases, which explains the reduction in real time from 0.396s at threshold 2,097,152 to 0.123s at 16,384. This
corresponds to an approximate 3.2x speedup, demonstrating the effectiveness of parallel execution.

However, the user and sys times tell a more complex story. As the threshold drops from 2,097,152 to 16,384, the total user time rises from 0.372s to 0.530s, and sys
time grows significantly from 0.018s to 0.154s. This is due to the overhead of the fork() and waitpid() system calls, introduced by frequent process creation and
synchronization. Each time a process is forked, the kernel must manage new process structures, and each waitpid involves context switching and process synchronization.

The results also demonstrate diminishing returns. While increasing parallelism initially reduces real time, beyond a certain point the overhead of managing many processes
begins to cancel out the benefits. This is evident in the small changes in real time at lower thresholds, where additional parallelism does not improve performance in
general.

The "sweet spot" in this experiment appears to be around the 16,384–65,536 range, where the benefits of parallel execution on multiple cores still outweigh the increasing
cost of process management. The slight fluctuations in real time (such as the jump at 131,072) are likely due to transient system load or the specific way the OS scheduler
distributed the processes at that moment. Therefore, we chose this range, where parallelism is maximized, maintaining the acceptable overhead.