# 系统异常事件

## 简介

目前，gala-gopher和gala-anteater均提供系统异常检测能力。其中gala-gopher支持用户在启动各个探针的时候，通过阈值(包括上下限)设置异常范围，探针会根据阈值判断某个指标是否异常，如果异常则上报异常事件；而gala-anteater是一款基于AI的操作系统异常检测平台，主要涵盖时序数据预处理、异常点发现、以及异常上报等功能，其能够提供更加准确地系统级异常检测能力。

gala-gopher和gala-anteater遵循统一的系统级异常数据格式规范，能够更好地兼容其他应用。

## 如何开启异常事件

gala-gopher如何开启异常事件
- gopher支持异常事件的探针参考[支持的异常事件](#支持的异常事件)。
- 探针启动参数开启异常事件上报 `-l WARN` 。
- 设置阈值，比如：设置资源利用率上限为80% `-U 80`，设置资源利用率下限为5% `-L 5` 。

> 注：
> 1. gala-anteater无需手动开启，其能够自动进行异常事件检测以及异常事件上报。
> 2. gala-gopher异常事件开关、阈值通过探针启动参数传递，探针启动参数参考[这里](https://gitee.com/openeuler/gala-gopher/blob/master/doc/conf_introduction.md#%E5%90%AF%E5%8A%A8%E5%8F%82%E6%95%B0%E4%BB%8B%E7%BB%8D)。

## gala-gopher支持的异常事件

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

| 异常事件名    | 事件信息                                                     | 输出参数          | 输入参数 | 异常等级 |
| ------------- | ------------------------------------------------------------ | ----------------- | -------- | -------- |
| tcp_oom       | TCP out of memory(%u).                                       | P1: error count   | NA       | WARN     |
| backlog_drops | TCP backlog queue drops(%u).                                 | P1: drops count   | [-D <>]  | WARN     |
| filter_drops  | TCP filter drops(%u).                                        | P1: drops count   | [-D <>]  | WARN     |
| syn_srtt      | TCP connection establish timed out(%u us).                   | P1: syn rtt times | [-T <>]  | WARN     |
| sk_drops      | Number of lost packets in the TCP protocol stack(%u).        | P1: drops count   | [-D <>]  | WARN     |
| lost_out      | Number of lost segments estimated by TCP congestion(%u).     | P1: drops count   | [-D <>]  | WARN     |
| sacked_out    | Number of out-of-order TCP packets (SACK) or number of repeated TCP ACKs (NO SACK)(%u). | P1: drops count   | [-D <>]  | WARN     |
| rcv_wnd       | TCP zero receive windows.                                    | NA                | NA       | WARN     |
| snd_wnd       | TCP zero send windows.                                       | NA                | NA       | WARN     |
| avl_snd_wnd   | TCP zero available send windows.                             | NA                | NA       | WARN     |

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

| 异常事件名          | 事件信息                                                  | 输出参数           | 输入参数 | 异常等级 |
| ------------------- | --------------------------------------------------------- | ------------------ | -------- | -------- |
| listendrop          | TCP listen drops(%lu).                                    | P1: drops count    | NA       | WARN     |
| accept_overflow     | TCP accept queue overflow(%lu).                           | P1: overflow count | NA       | WARN     |
| syn_overflow        | TCP syn queue overflow(%lu).                              | P1: overflow count | NA       | WARN     |
| passive_open_failed | TCP passive open failed(%lu).                             | P1: failed count   | NA       | WARN     |
| active_open_failed  | TCP active open failed(%lu).                              | P1: failed count   | NA       | WARN     |
| bind_rcv_drops      | UDP(S) queue drops(%lu).                                  | P1: drops count    | NA       | WARN     |
| udp_rcv_drops       | UDP(C) queue drops(%lu).                                  | P1: drops count    | NA       | WARN     |
| lost_synacks        | TCP connection setup failure due to loss of SYN/ACK(%lu). | P1: failed count   | NA       | WARN     |
| retran_synacks      | TCP SYN/ACK retransmission occurs.(%lu).                  | P1: failed count   | NA       | WARN     |

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
| sched_systime      | CPU %s sys-state.(CPU = %d, Comm = %s, PID = %u, Latency = %llums) |                                                              | NA       | WARN     |
| sched_syscall      | COMM: %s syscall %s.(CPU = %d, PID = %u, SYSID = %u, Latency = %llums, Delay = %llums) |                                                              | NA       | WARN     |

### BLOCK

| 异常事件名      | 事件信息                                                     | 输出参数 | 输入参数 | 异常等级 |
| --------------- | ------------------------------------------------------------ | -------- | -------- | :------- |
| latency_req_max | IO latency occured. (Block %d:%d, COMM %s, PID %u, op: %s, datalen %u, drv_latency %llu, dev_latency %llu) |          | [-T <>]  | WARN     |
| err_code        | IO errors occured. (Block %d:%d, COMM %s, PID %u, op: %s, datalen %u, err_code %d, scsi_err %d, scsi_tmout %d) |          | NA       | WARN     |

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


## gala-aneater 支持的异常事件

gala-anteater当前支持`应用级`和`系统级`两类异常检测任务，并上报对应异常事件。同时，对于每个异常事件，定义了统一的异常事件格式。

### 异常事件格式

gala-aneater全部异常事件遵循一种的异常事件格式，格式如下：

| 输出参数 	| 参数含义 	| 描述 	|
|:---:	|:---:	|---	|
| Timestamp 	| 时间戳 	| 13位long型数字 	|
| Attributes 	| 属性值 	| 包括:<br>1. entity_id: <machine_id>_<entiry_name>_<keys><br>2. event_id:   <timestame>_<entity_id><br>3. event_type: sys/app<br>4. event_source: gala-aneater 	|
| Resource 	| 异常事件主要信息 	| 包括：<br>1. metric: 异常指标名称<br>2. labels: 异常指标的标签信息<br>3. cause_metrics: 引起异常可能的原因<br>4. description: 异常指标的描述信息 	|
| SeverityText 	| 异常事件类型 	| INFO/WARN/ERROR/FATAL 	|
| SeverityNumber 	| 异常事件编号 	| 9, 13, 178, 21 ... 	|
| Body 	| 异常事件信息 	| string 	|

例如，系统级磁盘异常事件格式：
```json
{
    "Timestamp": 1670926603208,
    "Attributes": {
        "entity_id": "2c1c455d-24a5-897c-ea11-bc08f2d510da_disk_dm-9",
        "event_id": "1670926603208_2c1c455d-24a5-897c-ea11-bc08f2d510da_disk_dm-9",
        "event_type": "sys",
        "event_source": "gala-anteater"
    },
    "Resource": {
        "metric": "gala_gopher_disk_rspeed_kB",
        "labels": {
            "disk_name": "dm-9",
            "instance": "10.xxx.xxx.xxx:xx",
            "job": "ceph2-10.xxx.xxx.xxx",
            "machine_id": "2c1c455d-24a5-897c-ea11-bc08f2d510da"
        },
        "score": 0.6,
        "cause_metrics": [
            {
                "metric": "gala_gopher_disk_rareq",
                "labels": {
                    "disk_name": "dm-9",
                    "instance": "10.109.xxx.xxx:xx",
                    "job": "ceph2-10.109.xxx.xxx",
                    "machine_id": "2c1c455d-24a5-897c-ea11-bc08f2d510da"
                },
                "score": 5.03982338419878,
                "description": "The disk I/O await time performance deteriorates due to read saturation rise.(Disk = dm-9)"
            }
        ],
        "description": "Disk write await time is increasing!"
    },
    "SeverityText": "WARN",
    "SeverityNumber": 13,
    "Body": "Tue Dec 13 18:16:43 2022 WARN, SYS may be impacting performance issues.",
}
```

### 应用级

#### 1. SLI性能劣化

gala-anteater应用级异常事件，主要基于应用级SLI指标进行异常检测，当前使用的sli指标为。

| 异常事件名 | 观测实体 |            事件描述           | 异常等级 |
|:----------:|----------|:-----------------------------:|----------|
|  rtt_nsec  | SLI      | 应用级请求往返时延（RTT）异常 | WARN     |
|     tps    | SLI      |  应用级请求吞吐量（TPS）异常  | WARN     |

输出示例：
```json
{
    "Timestamp": 1670926723208,
    "Attributes": {
        "entity_id": "b59927a6-bce5-4f0a-a36e-18895ad59e6f_sli_2866035_14566_POSTGRE_0",
        "event_id": "1670926723208_b59927a6-bce5-4f0a-a36e-18895ad59e6f_sli_2866035_14566_POSTGRE_0",
        "event_type": "app",
        "event_source": "gala-anteater"
    },
    "Resource": {
        "metric": "gala_gopher_sli_tps",
        "labels": {
            "app": "POSTGRE",
            "datname": "postgres",
            "ins_id": "14566",
            "instance": "10.xxx.xxx.xxx:8888",
            "job": "vm05-10.xxx.xxx.xxx",
            "machine_id": "b59927a6-bce5-4f0a-a36e-18895ad59e6f",
            "method": "0",
            "server_ip": "172.xxx.xxx.xxx",
            "server_port": "5432",
            "tgid": "2866035"
        },
        "score": 0.36,
        "cause_metrics": [
            {
                "metric": "gala_gopher_block_latency_req_last",
                "labels": {
                    "blk_name": "vdb",
                    "disk_name": "vdb",
                    "first_minor": "16",
                    "instance": "10.xxx.xxx.xxx:8888",
                    "job": "vm05-10.xxx.xxx.xxx",
                    "machine_id": "b59927a6-bce5-4f0a-a36e-18895ad59e6f",
                    "major": "253"
                },
                "score": 12.84235139857697,
                "description": "block层request时延最近值异常"
            },
            {
                "metric": "gala_gopher_proc_ns_ext4_flush",
                "labels": {
                    "comm": "gaussdb",
                    "instance": "10.xxx.xxx.xxx:8888",
                    "job": "vm05-10.xxx.xxx.xxx",
                    "machine_id": "b59927a6-bce5-4f0a-a36e-18895ad59e6f",
                    "tgid": "2866035"
                },
                "score": 7.933597165856872,
                "description": "ext4文件系统flush操作时间(单位ns)异常"
            }
        ],
        "description": "sli tps 异常"
    },
    "SeverityText": "WARN",
    "SeverityNumber": 13,
    "Body": "Tue Dec 13 18:18:43 2022 WARN, APP may be impacting sli performance issues."
}
```

### 系统级

gala-anteater系统级异常事件，主要基于系统级相关性能指标，进行异常检测，并上报异常事件。目前，主要的系统级异常事件有如下几类。

#### 1. TCP建链性能劣化
上报TCP建链性能相关指标发生劣化，主要为：

| 异常事件名 | 观测实体 |      事件描述     | 异常等级 |
|:----------:|----------|:-----------------:|----------|
|  syn_srtt  | tcp_link | SYN数据包时延异常 | WARN     |

输出示例：
```json
{
    "Timestamp": 1670926603208,
    "Attributes": {
        "entity_id": "6489d66f-6fb7-4c31-8f52-bf349d9bd63e_tcp_link_3530555_0_10.xxx.xxx.xxx_192.xxx.xxx.xxx_0_22_2",
        "event_id": "1670926603208_6489d66f-6fb7-4c31-8f52-bf349d9bd63e_tcp_link_3530555_0_10.xxx.xxx.xxx_192.xxx.xxx.xxx_0_22_2",
        "event_type": "sys",
        "event_source": "gala-anteater"
    },
    "Resource": {
        "metric": "gala_gopher_tcp_link_syn_srtt",
        "labels": {
            "client_ip": "10.xxx.xxx.xxx",
            "client_port": "0",
            "comm": "sshd",
            "instance": "10.xxx.xxx.xxx:8888",
            "job": "vm03-10.xxx.xxx.xxx",
            "machine_id": "6489d66f-6fb7-4c31-8f52-bf349d9bd63e",
            "protocol": "2",
            "role": "0",
            "server_ip": "192.xxx.xxx.xxx",
            "server_port": "22",
            "tgid": "3530555"
        },
        "score": 0,
        "cause_metrics": [],
        "description": "RTT of syn packet(us): the max syn packets rtt is 29714 us"
    },
    "SeverityText": "WARN",
    "SeverityNumber": 13,
    "Body": "Tue Dec 13 18:16:43 2022 WARN, SYS may be impacting performance issues.",
    "cause_metric": {
        "description": "Unknown"
    }
}
```

#### 2. TCP传输性能劣化
上报TCP传输性能相关指标发生劣化，主要为：

| 异常事件名 | 观测实体 |            事件描述           | 异常等级 |
|:----------:|----------|:-----------------------------:|----------|
|    srtt    | tcp_link | TCP链接往返时延异常，性能劣化 | WARN     |


输出示例：
```json
{
    "Timestamp": 1670926843208,
    "Attributes": {
        "entity_id": "6489d66f-6fb7-4c31-8f52-bf349d9bd63e_tcp_link_3260713_0_10.xxx.xxx.xxx_172.xxx.xxx.xxx_0_5432_2",
        "event_id": "1670926843208_6489d66f-6fb7-4c31-8f52-bf349d9bd63e_tcp_link_3260713_0_10.xxx.xxx.xxx_172.xxx.xxx.xxx_0_5432_2",
        "event_type": "sys",
        "event_source": "gala-anteater"
    },
    "Resource": {
        "metric": "gala_gopher_tcp_link_srtt",
        "labels": {
            "client_ip": "10.xxx.xxx.xxx",
            "client_port": "0",
            "comm": "worker",
            "instance": "10.xxx.xxx.xxx:8888",
            "job": "vm03-10.xxx.xxx.xxx",
            "machine_id": "6489d66f-6fb7-4c31-8f52-bf349d9bd63e",
            "protocol": "2",
            "role": "0",
            "server_ip": "172.xxx.xxx.xxx",
            "server_port": "5432",
            "tgid": "3260713"
        },
        "score": 0.72,
        "cause_metrics": [],
        "description": "Smoothed Round Trip Time(us)"
    },
    "SeverityText": "WARN",
    "SeverityNumber": 13,
    "Body": "Tue Dec 13 18:20:43 2022 WARN, SYS may be impacting performance issues.",
}
```

#### 3. 系统I/O性能劣化
上报系统I/O性能相关指标发生劣化，主要为：

|    异常事件名   | 观测实体 |          事件描述          | 异常等级 |
|:---------------:|----------|:--------------------------:|----------|
| latency_req_max | block    | Block层I/O操作时延性能劣化 | WARN     |

输出示例：
```json
{
    "Timestamp": 1670926783208,
    "Attributes": {
        "entity_id": "2c1c455d-24a5-897c-ea11-bc08f2d510da_block_8_80",
        "event_id": "1670926783208_2c1c455d-24a5-897c-ea11-bc08f2d510da_block_8_80",
        "event_type": "sys",
        "event_source": "gala-anteater"
    },
    "Resource": {
        "metric": "gala_gopher_block_latency_req_max",
        "labels": {
            "blk_name": "sdf",
            "disk_name": "sdf",
            "first_minor": "80",
            "instance": "10.xxx.xxx.xxx:8888",
            "job": "ceph2-10.xxx.xxx.xxx",
            "machine_id": "2c1c455d-24a5-897c-ea11-bc08f2d510da",
            "major": "8"
        },
        "score": 0.36,
        "cause_metrics": [
            {
                "metric": "gala_gopher_block_read_bytes",
                "labels": {
                    "blk_name": "sdf",
                    "disk_name": "sdf",
                    "first_minor": "80",
                    "instance": "10.xxx.xxx.xxx:8888",
                    "job": "ceph2-10.xxx.xxx.xxx",
                    "machine_id": "2c1c455d-24a5-897c-ea11-bc08f2d510da",
                    "major": "8"
                },
                "score": 6.665321050421419,
                "description": "System performance deteriorates due to frequent read I/O operations.(Disk = sdf)"
            }
        ],
        "description": "Block I/O latency performance is deteriorating!"
    },
    "SeverityText": "WARN",
    "SeverityNumber": 13,
    "Body": "Tue Dec 13 18:19:43 2022 WARN, SYS may be impacting performance issues."
}
```

#### 4. 进程I/O性能劣化
上报进程I/O性能相关指标发生劣化，主要为：

|      异常事件名     | 观测实体 |                 事件描述                | 异常等级 |
|:-------------------:|----------|:---------------------------------------:|----------|
|     bio_latency     | proc     |       BIO层I/O操作延时高(单位：us)      | WARN     |
| less_4k_io_read     | proc     | BIO层小数据I/O读操作数量异常（小于4KB） | WARN     |
| less_4k_io_write    | proc     | BIO层小数据I/O写操作数量异常（小于4KB） | WARN     |
| greater_4k_io_read  | proc     | BIO层大数据I/O读操作数量异常（大于4KB） | WARN     |
| greater_4k_io_write | proc     | BIO层大数据写操作数量异常（大于4KB）    | WARN     |

输出示例：
```json
{
    "Timestamp": 1670926783208,
    "Attributes": {
        "entity_id": "b59927a6-bce5-4f0a-a36e-18895ad59e6f_proc_2869374",
        "event_id": "1670926783208_b59927a6-bce5-4f0a-a36e-18895ad59e6f_proc_2869374",
        "event_type": "sys",
        "event_source": "gala-anteater"
    },
    "Resource": {
        "metric": "gala_gopher_proc_greater_4k_io_read",
        "labels": {
            "comm": "gaussdb",
            "instance": "10.xxx.xxx.xxx:8888",
            "job": "vm05-10.xxx.xxx.xxx",
            "machine_id": "b59927a6-bce5-4f0a-a36e-18895ad59e6f",
            "tgid": "2869374"
        },
        "score": 0.52,
        "cause_metrics": [
            {
                "metric": "gala_gopher_proc_less_4k_io_read",
                "labels": {
                    "comm": "gaussdb",
                    "instance": "10.xxx.xxx.xxx:8888",
                    "job": "vm05-10.xxx.xxx.xxx",
                    "machine_id": "b59927a6-bce5-4f0a-a36e-18895ad59e6f",
                    "tgid": "2869374"
                },
                "score": 2.4876154256202576,
                "description": "System performance degrades due to frequent small I/O read operations.(Disk = , PID = 2869374, comm = gaussdb)"
            }
        ],
        "description": "Number of big I/O (greater than 4 KB) read operations at the BIO layer."
    },
    "SeverityText": "WARN",
    "SeverityNumber": 13,
    "Body": "Tue Dec 13 18:19:43 2022 WARN, SYS may be impacting performance issues."
}
```

#### 5. 磁盘读写时延性能劣化
上报磁盘读写时延性能相关指标发生劣化，主要为：

| 异常事件名 | 观测实体 |             事件描述             | 异常等级 |
|:----------:|----------|:--------------------------------:|----------|
|   r_await  | disk     | 磁盘读响应时间升高，性能发生劣化 | WARN     |
| w_await    | disk     | 磁盘写响应时间升高，性能发生劣化 | WARN     |

输出示例：
```json
{
    "Timestamp": 1670926723208,
    "Attributes": {
        "entity_id": "2cd02d44-24a5-a12b-ea11-610b749c10a9_disk_sdc",
        "event_id": "1670926723208_2cd02d44-24a5-a12b-ea11-610b749c10a9_disk_sdc",
        "event_type": "sys",
        "event_source": "gala-anteater"
    },
    "Resource": {
        "metric": "gala_gopher_disk_r_await",
        "labels": {
            "disk_name": "sdc",
            "instance": "10.xxx.xxx.xxx:8888",
            "job": "ceph1-10.xxx.xxx.xxx",
            "machine_id": "2cd02d44-24a5-a12b-ea11-610b749c10a9"
        },
        "score": 0.32,
        "cause_metrics": [
            {
                "metric": "gala_gopher_disk_wareq",
                "labels": {
                    "disk_name": "sdc",
                    "instance": "10.xxx.xxx.xxx:8888",
                    "job": "ceph1-10.xxx.xxx.xxx",
                    "machine_id": "2cd02d44-24a5-a12b-ea11-610b749c10a9"
                },
                "score": 7.573942408085913,
                "description": "The disk I/O await time performance deteriorates due to write saturation rise.(Disk = sdc)"
            }
        ],
        "description": "Disk read await time is increasing!"
    },
    "SeverityText": "WARN",
    "SeverityNumber": 13,
    "Body": "Tue Dec 13 18:18:43 2022 WARN, SYS may be impacting performance issues."
}
```