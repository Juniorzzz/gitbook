---
description: Update configuration of one or more containers
---

# update

Usage: docker update \[OPTIONS] CONTAINER \[CONTAINER...]

> \--blkio-weight uint16 Block IO (relative weight), between 10 and 1000, or 0 to disable (default 0)&#x20;
>
> \--cpu-period int Limit CPU CFS (Completely Fair Scheduler) period&#x20;
>
> \--cpu-quota int Limit CPU CFS (Completely Fair Scheduler) quota&#x20;
>
> \--cpu-rt-period int Limit the CPU real-time period in microseconds&#x20;
>
> \--cpu-rt-runtime int Limit the CPU real-time runtime in microseconds
>
> \-c, --cpu-shares int CPU shares (relative weight)&#x20;
>
> \--cpus decimal Number of CPUs&#x20;
>
> \--cpuset-cpus string CPUs in which to allow execution (0-3, 0,1)&#x20;
>
> \--cpuset-mems string MEMs in which to allow execution (0-3, 0,1)&#x20;
>
> \--kernel-memory bytes Kernel memory limit&#x20;
>
> \-m, --memory bytes Memory limit&#x20;
>
> \--memory-reservation bytes Memory soft limit&#x20;
>
> \--memory-swap bytes Swap limit equal to memory plus swap: '-1' to enable unlimited swap&#x20;
>
> \--pids-limit int Tune container pids limit (set -1 for unlimited)&#x20;
>
> \--restart string Restart policy to apply when a container exits

```
docker update --restart=always <CONTAINER ID>
```
