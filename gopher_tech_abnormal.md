# TCP（entity_name：tcp_link）

| metrics_name  | description                                | param             | level |
| ------------- | ------------------------------------------ | ----------------- | ----- |
| tcp_oom       | TCP out of memory(%u).                     | P1: error count   | WARN  |
| backlog_drops | TCP backlog queue drops(%u).               | P1: drops count   | WARN  |
| filter_drops  | TCP filter drops(%u).                      | P1: drops count   | WARN  |
| syn_srtt      | TCP connection establish timed out(%u us). | P1: syn rtt times | WARN  |

# ENDPOINT

| metrics_name        | description                     | param              | level |
| ------------------- | ------------------------------- | ------------------ | ----- |
| listendrop          | TCP listen drops(%lu).          | P1: drops count    | WARN  |
| accept_overflow     | TCP accept queue overflow(%lu). | P1: overflow count | WARN  |
| syn_overflow        | TCP syn queue overflow(%lu).    | P1: overflow count | WARN  |
| passive_open_failed | TCP passive open failed(%lu).   | P1: failed count   | WARN  |
| active_open_failed  | TCP active open failed(%lu).    | P1: failed count   | WARN  |
| bind_rcv_drops      | UDP(S) queue drops(%lu).        | P1: drops count    | WARN  |
| udp_rcv_drops       | UDP(C) queue drops(%lu).        | P1: drops count    | WARN  |



# THREAD（entity_name：task）

| metrics_name  | description                                                  | param                                                        | level |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ----- |
| off_cpu_ns    | Process(COMM:%s TID:%d) is preempted(COMM:%s PID:%d) and off-CPU %llu ns. | P1: process name P2: process id P3: process name P4: process id P5: off-cpu times | WARN  |
| iowait_us     | Process(COMM:%s TID:%d) iowait %llu us.                      | P1: process name P2: process id P3: io-wait times            | WARN  |
| hang_count    | Process(COMM:%s TID:%d) io hang %u.                          | P1: process name P2: process id P3: error count              | WARN  |
| bio_err_count | Process(COMM:%s TID:%d) bio error %u.                        | P1: process name P2: process id P3: error count              | WARN  |

# Process

| metrics_name       | description                                                  | param                                                        | level |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ----- |
| syscall_failed     | Process(COMM:%s PID:%u) syscall failed(SysCall-ID:%d RET:%d COUNT:%u). | P1: process name P2: process id P3: syscall no P4: syscall ret-code P5 failed count | WARN  |
| gethostname_failed | Process(COMM:%s PID:%u) gethostname failed(COUNT:%u).        | P1: process name P2: process id P3 failed count              | WARN  |

# BLOCK

| metrics_name         | description                                                  | param                                                        | level |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ----- |
| count_iscsi_err      | Iscsi errors(%llu) occured on Block(%s, disk %s).            | P1: block name P2: disk name                                 | WARN  |
| count_iscsi_tmout    | Iscsi timeout(%llu) occured on Block(%s, disk %s).           | P1: block name P2: disk name                                 | WARN  |
| latency_flush_jitter | Jitter latency of flush operation(%llu) exceeded threshold, occured on Block(%s, disk %s). | P1：flush jitter latency, unit is us P2: block name P3: disk name | WARN  |
| latency_flush_max    | Latency of flush operation(%llu) exceeded threshold, occured on Block(%s, disk %s). | P1：flush latency, unit is us P2: block name P3: disk name   | WARN  |
| latency_req_jitter   | Jitter latency of request operation(%llu) exceeded threshold, occured on Block(%s, disk %s). | P1：request jitter latency, unit is us P2: block name P3: disk name | WARN  |
| latency_req_max      | Latency of request operation(%llu) exceeded threshold, occured on Block(%s, disk %s). | P1：request latency, unit is us P2: block name P3: disk name | WARN  |

# DISK

| metrics_name    | description                     | param          | level |
| --------------- | ------------------------------- | -------------- | ----- |
| inode_userd_per | Too many Inodes consumed(%d%%). | P1: Percentage | WARN  |
| block_userd_per | Too many Blocks used(%d%%).     | P1: Percentage | WARN  |
| iostat_util     | Disk device saturated(%.2f%%).  | P1: Percentage | WARN  |



# NET

| metrics_name        | description                      | param           | level |
| ------------------- | -------------------------------- | --------------- | ----- |
| net_device_tx_drops | net device tx queue drops(%llu). | P1: drops count | WARN  |
| net_device_rx_drops | net device rx queue drops(%llu). | P1: drops count | WARN  |