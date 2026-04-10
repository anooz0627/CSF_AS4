CONTRIBUTIONS

TODO: write a brief summary of how each team member contributed to
the project.

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

The data shows a clear trend where decreasing the parallel threshold generally leads to a reduction in real (wall-clock) time. This improvement occurs because a lower threshold allows the quicksort algorithm to partition the work into more child processes earlier in the recursion. Since the data is mapped using mmap with MAP_SHARED, these child processes can work on independent segments of the array simultaneously across multiple CPU cores.

However, the user and sys times tell a more complex story. As the threshold drops from 2,097,152 to 16,384, the total user time increases from 0.372s to 0.530s, and sys time grows significantly from 0.018s to 0.154s. This is due to the overhead of the fork() and waitpid() system calls. Each time a process is forked, the kernel must manage new process structures, and each waitpid involves context switching and process synchronization.

The "sweet spot" in this experiment appears to be around the 16,384–65,536 range, where the benefits of parallel execution on multiple cores still outweigh the increasing cost of process management. The slight fluctuations in real time (such as the jump at 131,072) are likely due to transient system load or the specific way the OS scheduler distributed the processes at that moment. Ultimately, the parallel version at the lowest threshold (0.123s) is approximately 3.2x faster than the mostly sequential version (0.396s).