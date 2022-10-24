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

#### 输出格式

```json
{
	"Timestamp": <timestamp>,
	"event_id": "<timestamp>_<machine_id>_<entity_name>_<tgid>_<fd>",  # tgid-应用进程号  fd-应用的socket文件描述符
	"Attributes": {
		"entity_id": "<machine_id>_<entity_name>_<tgid>_<fd>",
		"event_id": "<timestamp>_<machine_id>_<entity_name>_<tgid>_<fd>",
		"event_type": "sys"							 # sys-表示异常事件类型为系统级 
	},
	"Resource": {
		"metrics": "gala_gopher_sli_<event_name>"      # event_name-异常事件名，参考上表第一列
	},
	"SeverityText": "WARN",
	"SeverityNumber": 13,
	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(<entity_id>) Process(TID:<tgid>, CIP(<c_ip>:<c_port>), SIP(<s_ip>:<s_port>)) SLI(<cmd>:<rtt_nsec>) exceed the threshold." # entity_id-<tgid>_<fd>  rtt_nsec-响应时延，单位ns
}
```

#### 输出示例

- ##### rtt_nsec

  sli探针监控并统计具体应用（如Redis）的响应时延，当监测到响应时延超过阈值时上报异常事件。用户需要在启动gala-gopher前手动通过`-T`参数设置阈值。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_sli_3739183_23",
  	"Attributes": {
  		"entity_id": "e473b23xxx_sli_3739183_23",
  		"event_id": "1661593284000_e473b23xxx_sli_3739183_23",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_sli_rtt_nsec"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_23) Process(TID:3739183, CIP(9.82.194.97:48202), SIP(9.82.206.89:3742)) SLI(SET:48678) exceed the threshold."
      # 示例的事件信息包含了Redis应用的进程号、发起请求的客户端以及redis-server的IP和port、具体的请求命令和本次响应时延(单位ns)
  }
  ```

### TCP_LINK

| 异常事件名    | 事件信息                                   | 输出参数          | 输入参数 | 异常等级 |
| ------------- | ------------------------------------------ | ----------------- | -------- | -------- |
| tcp_oom       | TCP out of memory(%u).                     | P1: error count   | NA       | WARN     |
| backlog_drops | TCP backlog queue drops(%u).               | P1: drops count   | [-D <>]  | WARN     |
| filter_drops  | TCP filter drops(%u).                      | P1: drops count   | [-D <>]  | WARN     |
| syn_srtt      | TCP connection establish timed out(%u us). | P1: syn rtt times | [-T <>]  | WARN     |

> 注：输入参数为NA表示不需要外部输入阈值参数，内部实现是根据指标值是否为0判断异常与否。

#### 输出格式

```json
{
	"Timestamp": <timestamp>,
	"event_id": "<timestamp>_<machine_id>_<entity_name>_<tgid>_<role>_<c_ip>_<s_ip>_<c_port>_<s_port>_<family>",  
    		           # role-客户端/服务端 c_ip/s_ip-对端IP/本地IP c_port/s_port-对端端口/本地端口 family-协议族如IPv4
	"Attributes": {
		"entity_id": "<machine_id>_<entity_name>_<tgid>_<role>_<c_ip>_<s_ip>_<c_port>_<s_port>_<family>",
		"event_id": "<timestamp>_<machine_id>_<entity_name>_<tgid>_<role>_<c_ip>_<s_ip>_<c_port>_<s_port>_<family>",
		"event_type": "sys"
	},
	"Resource": {
		"metrics": "gala_gopher_tcp_link_<event_name>"	# event_name-异常事件名，参见上表中第一列
	},
	"SeverityText": "WARN",
	"SeverityNumber": 13,
	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(<entity_id>) <descriptions>." # descriptions-事件信息，参见上表第二列 
}
```

#### 输出示例

- ##### tcp_oom

  tcpprobe探针会记录TCP内存占用量超过了系统设定的最大值的次数，也即检测到该条TCP连接内存不足的次数。该异常事件的阈值不需要用户手动配置，默认为0，即只要检测到TCP内存不足则上报异常事件。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2",
  	"Attributes": {
  		"entity_id": "e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2",
  		"event_id": "1661593284000_e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_tcp_link_tcp_oom"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_0_9.82.194.97_9.82.206.89_78202_3742_2) TCP out of memory(1)."									# 句尾括号中“1”表示检测到oom的次数为1
  }
  ```

- ##### backlog_drops

  tcpprobe探针会记录因backlog队列满导致的丢包次数，若判断该丢包次数大于用户设定的阈值则上报异常事件。用户需要在启动gala-gopher前通过`-D`参数设定该阈值的值，不设定该值则此项异常事件关闭。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2", 
  	"Attributes": {
  		"entity_id": "e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2",
  		"event_id": "1661593284000_e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_tcp_link_backlog_drops"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_0_9.82.194.97_9.82.206.89_78202_3742_2) TCP backlog queue drops(3)."										# 示例中设定阈值为2，句尾括号中“3”表示检测到此类丢包的次数为3
  }
  ```

- ##### filter_drops

  tcpprobe探针会记录经过过滤器filter丢弃的包数，当判断该丢包数大于用户设定的阈值则上报异常事件。用户需要在启动gala-gopher前通过`-D`参数设定该阈值的值，不设定该值则此项异常事件关闭。和backlog_drops异常事件共用同一个参数。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2", 
  	"Attributes": {
  		"entity_id": "e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2",
  		"event_id": "1661593284000_e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_tcp_link_filter_drops"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_0_9.82.194.97_9.82.206.89_78202_3742_2) TCP filter drops(4)." 									# 句尾括号中“4”表示检测到此类丢包的次数为4
  }
  ```

- ##### syn_srtt

  tcpprobe会记录服务端接收到SYN请求后，从发送SYN/ACK到接收到客户端ACK经过的时间，若判断该建链时间大于用户设置的阈值则上报异常事件。用户需要在启动gala-gopher之前通过`-T`参数设置阈值。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2", 
  	"Attributes": {
  		"entity_id": "e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2",
  		"event_id": "1661593284000_e473b23xxx_tcp_link_3739183_0_9.82.194.97_9.82.206.89_78202_3742_2",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_tcp_link_syn_srtt"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_0_9.82.194.97_9.82.206.89_78202_3742_2) TCP connection establish timed out(10 us)."
  }
  ```

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

#### 输出格式

```json
{
	"Timestamp": <timestamp>,
	"event_id": "<timestamp>_<machine_id>_<entity_name>_<tgid>_<s_ip>_<s_port>_<table_name>",  
    		                            # s_ip和s_port可能为空，具体参考示例  table_name-具体类型为listen|connect|bind|udp
	"Attributes": {
		"entity_id": "<machine_id>_<entity_name>_<tgid>_<s_ip>_<s_port>_<table_name>",
		"event_id": "<timestamp>_<machine_id>_<entity_name>_<tgid>_<s_ip>_<s_port>_<table_name>",
		"event_type": "sys"
	},
	"Resource": {
		"metrics": "gala_gopher_endpoint_<event_name>"	# event_name-异常事件名，参见上表中第一列
	},
	"SeverityText": "WARN",
	"SeverityNumber": 13,
	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(<entity_id>) <descriptions>." # descriptions-事件信息，参见上表第二列
														         # entity_id-<tgid>_<s_ip>_<s_port>_<table_name>
}
```

#### 输出示例

- ##### listendrop

  当检测到TCP accept丢弃次数大于0则上报异常事件。该异常事件的阈值不需要用户手动配置，默认为0。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_endpoint_3739183_*_3742_listen",
  	"Attributes": {
  		"entity_id": "e473b23xxx_endpoint_3739183_*_3742_listen",
  		"     event_id": "1661593284000_e473b23xxx_endpoint_3739183_*_3742_listen",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_endpoint_listendrop"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_*_3742_listen) TCP listen drops(1)." # 句尾括号中“1”即丢包数
  }
  ```

- ##### accept_overflow

  当检测到TCP 全连接队列溢出的次数大于0则上报异常事件。该异常事件的阈值不需要用户手动配置，默认为0。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_endpoint_3739183_*_3742_listen",
  	"Attributes": {
  		"entity_id": "e473b23xxx_endpoint_3739183_*_3742_listen",
  		"     event_id": "1661593284000_e473b23xxx_endpoint_3739183_*_3742_listen",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_endpoint_accept_overflow"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_*_3742_listen) TCP accept queue overflow(1)."
  }                                                                                       # 句尾括号中“1”表示队列溢出次数
  ```

- ##### syn_overflow

  当检测到TCP 半连接队列溢出的次数大于0则上报异常事件。该异常事件的阈值不需要用户手动配置，默认为0。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_endpoint_3739183_*_3742_listen",
  	"Attributes": {
  		"entity_id": "e473b23xxx_endpoint_3739183_*_3742_listen",
  		"     event_id": "1661593284000_e473b23xxx_endpoint_3739183_*_3742_listen",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_endpoint_syn_overflow"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_*_3742_listen) TCP syn queue overflow(1)."
  }
  ```

- ##### passive_open_failed

  当检测到TCP 被动发起的建链失败次数大于0则上报异常事件。该异常事件的阈值不需要用户手动配置，默认为0。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_endpoint_3739183_*_3742_listen",
  	"Attributes": {
  		"entity_id": "e473b23xxx_endpoint_3739183_*_3742_listen",
  		"     event_id": "1661593284000_e473b23xxx_endpoint_3739183_*_3742_listen",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_endpoint_passive_open_failed"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_*_3742_listen) TCP passive open failed(1)."
  }                                                                                           # 句尾括号中“1”表示失败次数
  ```

- ##### active_open_failed

  当检测到TCP 主动发起的建链失败次数大于0则上报异常事件。该异常事件的阈值不需要用户手动配置，默认为0。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_endpoint_3739183_9.82.206.89_0_connect",
  	"Attributes": {
  		"entity_id": "e473b23xxx_endpoint_3739183_9.82.206.89_0_connect",
  		"     event_id": "1661593284000_e473b23xxx_endpoint_3739183_9.82.206.89_0_connect",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_endpoint_active_open_failed"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_9.82.206.89_0_connect) TCP active open failed(1)."
  }
  ```

- ##### bind_rcv_drops

  当检测到UDP服务端接收失败的次数大于0则上报异常事件。该异常事件的阈值不需要用户手动配置，默认为0。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_endpoint_3739183_9.82.206.89_0_bind",
  	"Attributes": {
  		"entity_id": "e473b23xxx_endpoint_3739183_9.82.206.89_0_bind",
  		"     event_id": "1661593284000_e473b23xxx_endpoint_3739183_9.82.206.89_0_bind",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_endpoint_bind_rcv_drops"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_9.82.206.89_0_bind) UDP(S) queue drops(1)."
  }
  ```

- ##### udp_rcv_drops

  当检测到UDP客户端接收失败的次数大于0则上报异常事件。该异常事件的阈值不需要用户手动配置，默认为0。

  ```json
  {
  	"Timestamp": 1661593284000,
  	"event_id": "1661593284000_e473b23xxx_endpoint_3739183_9.82.206.89_0_udp",
  	"Attributes": {
  		"entity_id": "e473b23xxx_endpoint_3739183_9.82.206.89_0_udp",
  		"     event_id": "1661593284000_e473b23xxx_endpoint_3739183_9.82.206.89_0_udp",
  		"event_type": "sys"
  	},
  	"Resource": {
  		"metrics": "gala_gopher_endpoint_udp_rcv_drops"
  	},
  	"SeverityText": "WARN",
  	"SeverityNumber": 13,
  	"Body": "Sat Aug 27 17:41:24 2022 WARN Entity(3739183_9.82.206.89_0_udp) UDP(C) queue drops(1)."
  }
  ```

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