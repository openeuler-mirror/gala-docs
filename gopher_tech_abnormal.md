# gala-gopher系统异常事件

## 简介

gala-gopher提供系统异常检测能力，支持用户在启动各个探针的时候，通过阈值(包括上下限)设置异常范围，探针会根据阈值判断某个指标是否异常，如果异常则上报异常事件。

## 如何开启异常事件

- 支持异常事件的探针参考[支持的异常事件](#支持的异常事件)。
- 探针启动参数开启异常事件上报 `-l WARN` 。
- 设置阈值，比如：设置资源利用率上限为80% `-U 80`，设置资源利用率下限为5% `-L 5` 。

> 注：异常事件开关、阈值通过探针启动参数传递，探针启动参数参考[这里](https://gitee.com/openeuler/gala-gopher/blob/master/doc/conf_introduction.md#%E5%90%AF%E5%8A%A8%E5%8F%82%E6%95%B0%E4%BB%8B%E7%BB%8D)。

## 支持的异常事件

本章以观测实体（`entity_name`）的粒度来介绍其支持的异常事件。

### SLI

| 异常事件名           | 事件信息                                                     | 输出参数                                                     | 输入参数 | 异常等级 |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------- | -------- |
| rtt_nsec（Redis/PG） | Process(TID:%d, CIP(%s:%u), SIP(%s:%u)) SLI(%s:%llu) exceed the threshold. | P1: process  ID P2: client ip and port P3: server ip and port P4 command and SLI | [-T <>]  | WARN     |

### TCP_LINK

| 异常事件名    | 事件信息                                   | 输出参数          | 输入参数 | 异常等级 |
| ------------- | ------------------------------------------ | ----------------- | -------- | -------- |
| tcp_oom       | TCP out of memory(%u).                     | P1: error count   | NA       | WARN     |
| backlog_drops | TCP backlog queue drops(%u).               | P1: drops count   | [-D <>]  | WARN     |
| filter_drops  | TCP filter drops(%u).                      | P1: drops count   | [-D <>]  | WARN     |
| syn_srtt      | TCP connection establish timed out(%u us). | P1: syn rtt times | [-T <>]  | WARN     |

> 注：输入参数为NA表示不需要外部输入阈值参数，内部实现是根据指标值是否为0判断异常与否。

### ENDPOINT

| 异常事件名          | 事件信息                        | 输出参数           | 输入参数 | 异常等级 |
| ------------------- | ------------------------------- | ------------------ | -------- | -------- |
| listendrop          | TCP listen drops(%lu).          | P1: drops count    | NA       | WARN     |
| accept_overflow     | TCP accept queue overflow(%lu). | P1: overflow count | NA       | WARN     |
| syn_overflow        | TCP syn queue overflow(%lu).    | P1: overflow count | NA       | WARN     |
| passive_open_failed | TCP passive open failed(%lu).   | P1: failed count   | NA       | WARN     |
| active_open_failed  | TCP active open failed(%lu).    | P1: failed count   | NA       | WARN     |
| bind_rcv_drops      | UDP(S) queue drops(%lu).        | P1: drops count    | NA       | WARN     |
| udp_rcv_drops       | UDP(C) queue drops(%lu).        | P1: drops count    | NA       | WARN     |

### THREAD

| 异常事件名 | 事件信息                                                     | 输出参数                                                     | 输入参数 | 异常等级 |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------- | -------- |
| off_cpu_ns | Process(COMM:%s TID:%d) is preempted(COMM:%s PID:%d) and off-CPU %llu ns. | P1: process name P2: process id P3: process name P4: process id P5: off-cpu times | [-O <>]  | WARN     |

### PROC

| 异常事件名         | 事件信息                                                     | 输出参数                                                     | 输入参数 | 异常等级 |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | -------- | -------- |
| syscall_failed     | Process(COMM:%s PID:%u) syscall failed(SysCall-ID:%d RET:%d COUNT:%u). | P1: process name P2: process id P3: syscall no P4: syscall ret-code P5 failed count | NA       | WARN     |
| gethostname_failed | Process(COMM:%s PID:%u) gethostname failed(COUNT:%u).        | P1: process name P2: process id P3 failed count              | NA       | WARN     |
| iowait_us          | Process(COMM:%s PID:%u) iowait %llu us.                      | P1: process name P2: process id P3: io-wait times            | [-T <>]  | WARN     |
| hang_count         | Process(COMM:%s PID:%u) hang count %u.                       | P1: process name P2: process id P3: error count              | NA       | WARN     |
| bio_err_count      | Process(COMM:%s PID:%u) bio error %u.                        | P1: process name P2: process id P3: error count              | NA       | WARN     |

### BLOCK

| 异常事件名           | 事件信息                                                     | 输出参数                                                     | 输入参数 | 异常等级 |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------- | :------- |
| count_iscsi_err      | Iscsi errors(%llu) occured on Block(%s, disk %s).            | P1: block name P2: disk name                                 | NA       | WARN     |
| count_iscsi_tmout    | Iscsi timeout(%llu) occured on Block(%s, disk %s).           | P1: block name P2: disk name                                 | NA       | WARN     |
| latency_flush_jitter | Jitter latency of flush operation(%llu) exceeded threshold, occured on Block(%s, disk %s). | P1：flush jitter latency, unit is us P2: block name P3: disk name | [-J <>]  | WARN     |
| latency_flush_max    | Latency of flush operation(%llu) exceeded threshold, occured on Block(%s, disk %s). | P1：flush latency, unit is us P2: block name P3: disk name   | [-T <>]  | WARN     |
| latency_req_jitter   | Jitter latency of request operation(%llu) exceeded threshold, occured on Block(%s, disk %s). | P1：request jitter latency, unit is us P2: block name P3: disk name | [-J <>]  | WARN     |
| latency_req_max      | Latency of request operation(%llu) exceeded threshold, occured on Block(%s, disk %s). | P1：request latency, unit is us P2: block name P3: disk name | [-T <>]  | WARN     |

### DISK

| 异常事件名  | 事件信息                       | 输出参数       | 输入参数 | 异常等级 |
| ----------- | ------------------------------ | -------------- | -------- | -------- |
| iostat_util | Disk device saturated(%.2f%%). | P1: Percentage | [-U <>]  | WARN     |

### DF

| 异常事件名      | 事件信息                        | 输出参数       | 输入参数 | 异常等级 |
| --------------- | ------------------------------- | -------------- | -------- | -------- |
| inode_userd_per | Too many Inodes consumed(%d%%). | P1: Percentage | [-U <>]  | WARN     |
| block_userd_per | Too many Blocks used(%d%%).     | P1: Percentage | [-U <>]  | WARN     |

### NIC

| 异常事件名           | 事件信息                          | 输出参数         | 输入参数 | 异常等级 |
| -------------------- | --------------------------------- | ---------------- | -------- | -------- |
| net_device_tx_drops  | net device tx queue drops(%llu).  | P1: drops count  | [-D <>]  | WARN     |
| net_device_rx_drops  | net device rx queue drops(%llu).  | P1: drops count  | [-D <>]  | WARN     |
| net_device_tx_errors | net device tx queue errors(%llu). | P1: errors count | [-D <>]  | WARN     |
| net_device_rx_errs   | net device tx queue errors(%llu). | P1: errors count | [-D <>]  | WARN     |

### CPU

| 异常事件名 | 事件信息                          | 输出参数       | 输入参数 | 异常等级 |
| ---------- | --------------------------------- | -------------- | -------- | -------- |
| used_per   | Too high cpu utilization(%.2f%%). | P1: Percentage | [-U <>]  | WARN     |