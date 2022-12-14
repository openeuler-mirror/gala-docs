# TCP（entity_name：tcp_link）

| metrics_name        | table_name        | metrics_type | unit               | KPI  | metrics description                                          |
| ------------------- | ----------------- | ------------ | ------------------ | ---- | ------------------------------------------------------------ |
| tgid                |                   | key          |                    |      | 进程ID                                                       |
| role                |                   | key          |                    |      | 客户端/服务端                                                |
| client_ip           |                   | key          |                    |      | 客户端：本地IP；服务端：对端IP                               |
| server_ip           |                   | key          |                    |      | 客户端：对端IP；服务端：本地IP                               |
| client_port         |                   | key          |                    |      | 客户端：本地端口；服务端：对端端口                           |
| server_port         |                   | key          |                    |      | 客户端：对端端口；服务端：本地端口                           |
| protocol            |                   | key          |                    |      | 协议族（IPv4、IPv6）                                         |
| rx_bytes            | tcp_tx_rx(0x8)    | Gauge        | bytes              | Y    | rx   bytes                                                   |
| tx_bytes            | tcp_tx_rx(0x8)    | Gauge        | bytes              | Y    | tx   bytes                                                   |
| rto                 | tcp_rate(0x20)    | Gauge        |                    |      | Retransmission   timeOut(us)                                 |
| ato                 | tcp_rate(0x20)    | Gauge        |                    |      | Estimated   value of delayed ACK(us)                         |
| srtt                | tcp_rtt(0x4)      | Gauge        | us                 | Y    | Smoothed   Round Trip Time(us).                              |
| snd_ssthresh        | tcp_rate(0x20)    | Gauge        |                    |      | Slow   start threshold for congestion control.               |
| rcv_ssthresh        | tcp_rate(0x20)    | Gauge        |                    |      | Current   receive window size.                               |
| snd_cwnd            | tcp_windows(0x2)  | Gauge        |                    |      | Congestion   Control Window Size.                            |
| advmss              | tcp_rate(0x20)    | Gauge        |                    |      | Local   MSS upper limit.                                     |
| reordering          | tcp_windows(0x2)  | Gauge        |                    |      | Segments   to be reordered.                                  |
| rcv_rtt             | tcp_rtt(0x4)      | Gauge        | us                 |      | Receive   end RTT (unidirectional measurement).              |
| rcv_space           | tcp_rate(0x20)    | Gauge        |                    |      | Current   receive buffer size.                               |
| notsent_bytes       | tcp_windows(0x2)  | Gauge        | bytes              |      | Number   of bytes not sent currently.                        |
| notack_bytes        | tcp_windows(0x2)  | Gauge        | bytes              |      | Number   of bytes not ack currently.                         |
| snd_wnd             | tcp_windows(0x2)  | Gauge        |                    |      | Size of TCP send window.                                     |
| rcv_wnd             | tcp_windows(0x2)  | Gauge        |                    |      | Size of TCP receive window.                                  |
| avl_snd_wnd         | tcp_windows(0x2)  | Gauge        |                    |      | Size of TCP available send window.                           |
| delivery_rate       | tcp_rate(0x20)    | Gauge        |                    |      | Current   transmit rate (multiple different from the actual value). |
| busy_time           | tcp_rate(0x20)    | Gauge        |                    |      | Time   (jiffies) busy sending data.                          |
| rwnd_limited        | tcp_rate(0x20)    | Gauge        |                    |      | Time   (jiffies) limited by receive window.                  |
| sndbuf_limited      | tcp_rate(0x20)    | Gauge        |                    |      | Time   (jiffies) limited by send buffer.                     |
| pacing_rate         | tcp_rate(0x20)    | Gauge        | bytes   per second |      | TCP   pacing rate, bytes per second                          |
| max_pacing_rate     | tcp_rate(0x20)    | Gauge        | bytes   per second |      | MAX   TCP pacing rate, bytes per second                      |
| sk_err_que_size     | tcp_sockbuf(0x10) | Gauge        |                    |      | Size   of error queue in sock.                               |
| sk_rcv_que_size     | tcp_sockbuf(0x10) | Gauge        |                    |      | Size   of receive queue in sock.                             |
| sk_wri_que_size     | tcp_sockbuf(0x10) | Gauge        |                    |      | Size   of write queue in sock.                               |
| syn_srtt            | tcp_srtt(0x40)    | Gauge        | us                 | Y    | RTT   of syn packet(us).                                     |
| sk_backlog_size     | tcp_sockbuf(0x10) | Gauge        |                    |      | Length of the queue used by the receive buffer.              |
| sk_omem_size        | tcp_sockbuf(0x10) | Gauge        | bytes              |      | Memory used by other caches.                                 |
| sk_forward_size     | tcp_sockbuf(0x10) | Gauge        | bytes              |      | Size of the pre-allocated memory of the sending cache.       |
| sk_wmem_size        | tcp_sockbuf(0x10) | Gauge        | bytes              |      | Used memory size of the sending cache.                       |
| sk_rcvbuf           | tcp_sockbuf(0x10) | Gauge        | bytes              |      | Byte length of the RX buffer.                                |
| sk_sndbuf           | tcp_sockbuf(0x10) | Gauge        | bytes              |      | Byte length of the TX buffer.                                |
| segs_in             | tcp_tx_rx(0x8)    | Counter      | segs               |      | total   number of segments received                          |
| segs_out            | tcp_tx_rx(0x8)    | Counter      | segs               |      | total   number of segments sent                              |
| retran_packets      | tcp_abn(0x01)     | Gauge        |                    | Y    | total number of retrans                                      |
| backlog_drops       | tcp_abn(0x01)     | Gauge        |                    | Y    | drops   caused by backlog queue full                         |
| sk_drops            | tcp_abn(0x01)     | Counter      |                    | Y    | Number of lost packets in the TCP protocol stack             |
| lost_out            | tcp_abn(0x01)     | Gauge        | segs               |      | Number of lost segments estimated by TCP congestion          |
| sacked_out          | tcp_abn(0x01)     | Gauge        | segs               |      | Number of out-of-order TCP packets (SACK) or number of repeated TCP ACKs (NO SACK) |
| filter_drops        | tcp_abn(0x01)     | Gauge        |                    |      | drops   caused by socket filter                              |
| tmout_count         | tcp_abn(0x01)     | Gauge        |                    |      | counter   of tcp link timeout                                |
| snd_buf_limit_count | tcp_abn(0x01)     | Gauge        |                    |      | counter   of limits when allocate wmem                       |
| rmem_scheduls       | tcp_abn(0x01)     | Gauge        |                    |      | rmem   is not enough                                         |
| tcp_oom             | tcp_abn(0x01)     | Gauge        |                    |      | tcp   out of memory                                          |
| send_rsts           | tcp_abn(0x01)     | Gauge        |                    |      | send_rsts                                                    |
| receive_rsts        | tcp_abn(0x01)     | Gauge        |                    |      | receive_rsts                                                 |
| sk_err              | tcp_abn(0x01)     | Gauge        |                    |      | sk_err                                                       |
| sk_err_soft         | tcp_abn(0x01)     | Gauge        |                    |      | sk_err_soft                                                  |

# ENDPOINT

| metrics_name        | table_name | metrics_type | unit  | KPI  | metrics description                              |
| ------------------- | ---------- | ------------ | ----- | ---- | ------------------------------------------------ |
| tgid                |            | key          |       |      | 进程ID                                           |
| s_addr              |            | key          |       |      | udp/tcp   本地地址                               |
| s_port              |            | key          |       |      | listen   port（只有listen对象存在该label）       |
| ep_type             |            | key          |       |      | listen/connect/udp/bind                          |
| listendrop          | listen     | Gauge        |       | Y    | TCP   accept丢弃次数（只有listen对象存在）       |
| accept_overflow     | listen     | Gauge        |       | Y    | TCP   accept队列溢出次数                         |
| syn_overflow        | listen     | Gauge        |       | Y    | TCP   syn队列溢出次数                            |
| passive_open        | listen     | Gauge        |       | Y    | tcp被动发起的建链次数（只有listen对象存在）      |
| passive_open_failed | listen     | Gauge        |       | Y    | tcp被动发起的建链失败次数（只有listen对象存在）  |
| retran_synacks      | listen     | Gauge        |       |      | tcp   synack重传报文数                           |
| lost_synacks        | listen     | Gauge        |       |      | TCP synack报文丢失导致的建链失败次数             |
| active_open         | connect    | Gauge        |       |      | tcp主动发起的建链次数（只有connect对象存在）     |
| active_open_failed  | connect    | Gauge        |       |      | tcp主动发起的建链失败次数（只有connect对象存在） |
| bind_rcv_drops      | bind       | Gauge        |       | Y    | UDP接收失败次数（udp/bind对象存在）              |
| bind_sends          | bind       | Gauge        | bytes | Y    | UDP发送长度（udp/bind对象存在）                  |
| bind_rcvs           | bind       | Gauge        | bytes | Y    | UDP接收长度（udp/bind对象存在）                  |
| bind_err            | bind       | Gauge        |       |      | UDP接收失败错误码（udp/bind对象存在）            |
| udp_rcv_drops       | udp        | Gauge        |       | Y    | UDP接收失败次数（udp/bind对象存在）              |
| udp_sends           | udp        | Gauge        | bytes | Y    | UDP发送长度（udp/bind对象存在）                  |
| udp_rcvs            | udp        | Gauge        | bytes | Y    | UDP接收长度（udp/bind对象存在）                  |
| udp_err             | udp        | Gauge        |       |      | UDP接收失败错误码（udp/bind对象存在）            |

# QDISC

| metrics_name | table_name | metrics_type | unit | KPI  | metrics description        |
| ------------ | ---------- | ------------ | ---- | ---- | -------------------------- |
| dev_name     | qdisc      | key          |      |      | 网卡设备名                 |
| handle       | qdisc      | key          |      |      | 设备句柄                   |
| ifindex      | qdisc      | key          |      |      | Interface   index of qidsc |
| kind         | qdisc      | label        |      |      | Kind   of qidsc            |
| netns        | qdisc      | label        |      |      | net   namespace            |
| qlen         | qdisc      | Gauge        |      |      | 队列长度                   |
| backlog      | qdisc      | Gauge        |      | Y    | backlog队列长度            |
| drops        | qdisc      | Counter      |      | Y    | 丢包数量                   |
| requeues     | qdisc      | Counter      |      | Y    | Requeues   count egress    |
| overlimits   | qdisc      | Counter      |      | Y    | 溢出数量                   |

# THREAD（entity_name：task）

| metrics_name    | table_name | metrics_type | unit | KPI  | metrics description                                          |
| --------------- | ---------- | ------------ | ---- | ---- | ------------------------------------------------------------ |
| pid             | thread     | key          |      |      | 线程PID                                                      |
| tgid            | thread     | label        |      |      | 所属进程ID                                                   |
| comm            | thread     | label        |      |      | 线程所属进程名称                                             |
| off_cpu_ns      | thread     | Gauge        | ns   | Y    | task调度offcpu的最大时间，统计方式：      1. KPROBE finish_task_switch 获取入参prev   task（pid）以及当前时间，当前CPU信息（bpf_get_smp_processor_id()），记录MAP（pid/cpu作为key）；      2. finish_task_switch   中bpf_get_current_pid_tgid获取当前pid，以及当前CPU信息（bpf_get_smp_processor_id()），匹配步骤1中的数据以及计算时间差，得出一次offcpu时间。      注意：      1. 过滤idle(pid=0)      2. 只记录offcpu最大值 |
| migration_count | thread     | Gauge        |      |      | task   CPU之间迁移次数                                       |

# Process（entity_name：proc）

| metrics_name          | table_name               | metrics_type | unit | KPI  | metrics description                                          |
| --------------------- | ------------------------ | ------------ | ---- | ---- | ------------------------------------------------------------ |
| tgid                  |                          | key          |      |      | 进程ID                                                       |
| ppid                  | system_proc              | label        |      |      | 父进程ID                                                     |
| pgid                  | system_proc              | label        |      |      | 进程组ID                                                     |
| comm                  |                          | label        |      |      | 执行程序名称                                                 |
| cmdline               | system_proc              | label        |      |      | 执行程序命令(包括配置)                                       |
| container_id          | system_proc              | label        |      |      | 进程归属的容器实例ID（简写）                                 |
| shared_dirty_size     | system_proc              | Gauge        |      |      | 进程共享属性的dirty   page size                              |
| shared_clean_size     | system_proc              | Gauge        |      |      | 进程共享属性的clean   page size                              |
| private_dirty_size    | system_proc              | Gauge        |      |      | 进程私有属性的dirty   page size                              |
| private_clean_size    | system_proc              | Gauge        |      |      | 进程私有属性的clean   page size                              |
| referenced_size       | system_proc              | Gauge        |      |      | 进程当前已引用的page   size                                  |
| lazyfree_size         | system_proc              | Gauge        |      |      | 进程延迟释放内存的size                                       |
| swap_data_size        | system_proc              | Gauge        |      |      | 进程swap区间数据size                                         |
| swap_data_pss_size    | system_proc              | Gauge        |      |      | 进程物理内存swap区间数据size                                 |
| fd_count              | system_proc              | Gauge        |      | Y    | 进程文件句柄                                                 |
| fd_free_per           | system_proc              | Gauge        |      |      | 进程剩余FD资源占比%                                          |
| utime_jiffies         | system_proc              | Gauge        |      | Y    | 进程用户运行时间                                             |
| stime_jiffies         | system_proc              | Gauge        |      | Y    | 进程系统态运行时间                                           |
| minor pagefault_count | system_proc              | Gauge        |      |      | 进程轻微pagefault次数（无需从磁盘拷贝）                      |
| major pagefault_count | system_proc              | Gauge        |      |      | 进程严重pagefault次数（需从磁盘拷贝）                        |
| vm_size               | system_proc              | Gauge        |      | Y    | 进程当前虚拟地址空间大小                                     |
| pm_size               | system_proc              | Gauge        |      | Y    | 进程当前物理地址空间大小                                     |
| rchar_bytes           | system_proc              | Gauge        |      |      | 进程系统调用至FS的读字节数                                   |
| wchar_bytes           | system_proc              | Gauge        |      |      | 进程系统调用至FS的写字节数                                   |
| syscr_count           | system_proc              | Gauge        |      |      | 进程read()/pread()执行次数                                   |
| syscw_count           | system_proc              | Gauge        |      |      | 进程write()/pwrite()执行次数                                 |
| read_bytes            | system_proc              | Gauge        |      |      | 进程实际从磁盘读取的字节数                                   |
| write_bytes           | system_proc              | Gauge        |      |      | 进程实际从磁盘写入的字节数      （page cache情况下，该字段进表示设置dirty page的size） |
| cancelled_write_bytes | system_proc              | Gauge        |      |      | 参考proc_write_bytes，因为存在page   cache      如果write操作结束后，又发生文件被删除事件，会导致diry page并未写入磁盘，所以存在取消写的字节数统计 |
| ns_ext4_read          | proc_ext4(0x20)          | Gauge        | ns   |      | ext4文件系统读操作时间，单位ns                               |
| ns_ext4_write         | proc_ext4(0x20)          | Gauge        | ns   |      | ext4文件系统写操作时间，单位ns                               |
| ns_ext4_flush         | proc_ext4(0x20)          | Gauge        | ns   |      | ext4文件系统flush操作时间，单位ns                            |
| ns_ext4_open          | proc_ext4(0x20)          | Gauge        | ns   |      | ext4文件系统open操作时间，单位ns                             |
| ns_overlay_read       | proc_overlay(0x40)       | Gauge        | ns   |      | overlayfs文件系统读操作时间，单位ns                          |
| ns_overlay_write      | proc_overlay(0x40)       | Gauge        | ns   |      | overlayfs文件系统写操作时间，单位ns                          |
| ns_overlay_flush      | proc_overlay(0x40)       | Gauge        | ns   |      | overlayfs文件系统flush操作时间，单位ns                       |
| ns_overlay_open       | proc_overlay(0x40)       | Gauge        | ns   |      | overlayfs文件系统open操作时间，单位ns                        |
| ns_tmpfs_read         | proc_tmpfs(0x80)         | Gauge        | ns   |      | tmpfs文件系统读操作时间，单位ns                              |
| ns_tmpfs_write        | proc_tmpfs(0x80)         | Gauge        | ns   |      | tmpfs文件系统写操作时间，单位ns                              |
| ns_tmpfs_flush        | proc_tmpfs(0x80)         | Gauge        | ns   |      | tmpfs文件系统flush操作时间，单位ns                           |
| reclaim_ns            | proc_page(0x100)         | Gauge        | ns   |      | 进程触发的page回收时间（执行SWAP操作），单位ns               |
| access_pagecache      | proc_page(0x100)         | Gauge        |      |      | 进程触发的页面访问次数                                       |
| mark_buffer_dirty     | proc_page(0x100)         | Gauge        |      |      | 进程触发的   page buffer置脏次数                             |
| load_page_cache       | proc_page(0x100)         | Gauge        |      |      | 进程触发的   page 加入page cache次数                         |
| mark_page_dirty       | proc_page(0x100)         | Gauge        |      |      | 进程触发的   page 置脏次数                                   |
| ns_gethostname        | proc_dns(0x200)          | Gauge        | ns   |      | 进程获取DNS域名对应的地址，单位ns                            |
| gethostname_failed    | proc_dns(0x200)          | Gauge        |      |      | 进程获取DNS域名失败次数                                      |
| ns_mount              | proc_syscall_io(0x02)    | Gauge        | ns   |      | 进程系统调用mount时长，单位ns                                |
| ns_umount             | proc_syscall_io(0x02)    | Gauge        | ns   |      | 进程系统调用umount时长，单位ns                               |
| ns_read               | proc_syscall_io(0x02)    | Gauge        | ns   |      | 进程系统调用read时长，单位ns                                 |
| ns_write              | proc_syscall_io(0x02)    | Gauge        | ns   |      | 进程系统调用write时长，单位ns                                |
| ns_fsync              | proc_syscall_io(0x02)    | Gauge        | ns   |      | 进程系统调用fsync时长，单位ns                                |
| ns_sendmsg            | proc_syscall_net(0x04)   | Gauge        | ns   |      | 进程系统调用sendmsg时长，单位ns                              |
| ns_recvmsg            | proc_syscall_net(0x04)   | Gauge        | ns   |      | 进程系统调用recvmsg时长，单位ns                              |
| ns_sched_yield        | proc_syscall_sched(0x08) | Gauge        | ns   |      | 进程系统调用sched_yield时长，单位ns                          |
| ns_futex              | proc_syscall_sched(0x08) | Gauge        | ns   |      | 进程系统调用futex时长，单位ns                                |
| ns_epoll_wait         | proc_syscall_sched(0x08) | Gauge        | ns   |      | 进程系统调用epoll_wait时长，单位ns                           |
| ns_epoll_pwait        | proc_syscall_sched(0x08) | Gauge        | ns   |      | 进程系统调用epoll_pwait时长，单位ns                          |
| ns_fork               | proc_syscall_fork(0x10)  | Gauge        | ns   |      | 进程系统调用fork时长，单位ns                                 |
| ns_vfork              | proc_syscall_fork(0x10)  | Gauge        | ns   |      | 进程系统调用vfork时长，单位ns                                |
| ns_clone              | proc_syscall_fork(0x10)  | Gauge        | ns   |      | 进程系统调用clone时长，单位ns                                |
| syscall_failed        | proc_syscall (0x01)      | Gauge        |      | Y    | 进程系统调用失败次数                                         |
| less_4k_io_read       | proc_io(0x400)           | Gauge        |      |      | Number of small I/O (less than 4 KB) read operations at the BIO layer. |
| less_4k_io_write      | proc_io(0x400)           | Gauge        |      |      | Number of small I/O (less than 4 KB) write operations at the BIO layer. |
| greater_4k_io_read    | proc_io(0x400)           | Gauge        |      |      | Number of big I/O (greater than 4 KB) read operations at the BIO layer. |
| greater_4k_io_write   | proc_io(0x400)           | Gauge        |      |      | Number of big I/O (greater than 4 KB) write operations at the BIO layer. |
| bio_latency           | proc_io(0x400)           | Gauge        | ns   |      | I/O operation delay at the BIO layer (unit: us).             |
| bio_err_count         | proc_io(0x400)           | Gauge        |      |      | Number of I/O operation failures at the BIO layer.           |
| hang_count            | proc_io(0x400)           | Gauge        |      |      | Number of process hang times.                                |
| iowait_us             | proc_io(0x400)           | Gauge        | us   |      | Process IO_wait time (unit: us).                             |
| sched_systime         | sched_systime            | Gauge        | us   |      | Duration of the process in the system state due to scheduling delay. |
| sched_syscall         | sched_syscall            | Gauge        | us   |      | Process syscall delay due to scheduling preemption.          |

# BLOCK

| metrics_name          | table_name         | metrics_type | unit  | KPI  | metrics description             |
| --------------------- | ------------------ | ------------ | ----- | ---- | ------------------------------- |
| major                 | block              | key          |       |      | 块对象编号                      |
| first_minor           | block              | key          |       |      | 块对象编号                      |
| blk_type              | block              | label        |       |      | 块对象类型（比如disk,   part）  |
| blk_name              | block              | label        |       |      | 块对象名称                      |
| disk_name             | block              | label        |       |      | 所属磁盘名称                    |
| latency_req_max       | io_latency(0x01)   | Gauge        | us    | Y    | block层I/O操作时延最大值        |
| latency_req_last      | io_latency(0x01)   | Gauge        | us    |      | block层I/O操作时延最近值        |
| latency_req_sum       | io_latency(0x01)   | Gauge        | us    |      | block层I/O操作时延总计值        |
| latency_req_jitter    | io_latency(0x01)   | Gauge        | us    |      | block层I/O操作时延抖动          |
| count_latency_req     | io_latency(0x01)   | Gauge        |       |      | block层I/O操作操作次数          |
| latency_driver_max    | io_latency(0x01)   | Gauge        | us    |      | 驱动层时延最大值                |
| latency_driver_last   | io_latency(0x01)   | Gauge        | us    |      | 驱动层时延最近值                |
| latency_driver_sum    | io_latency(0x01)   | Gauge        | us    |      | 驱动层时延最总计值              |
| latency_driver_jitter | io_latency(0x01)   | Gauge        | us    |      | 驱动层时延抖动                  |
| count_latency_driver  | io_latency(0x01)   | Gauge        |       |      | 驱动层操作次数                  |
| latency_device_max    | io_latency(0x01)   | Gauge        | us    | Y    | 设备层时延最大值                |
| latency_device_last   | io_latency(0x01)   | Gauge        | us    |      | 设备层时延最近值                |
| latency_device_sum    | io_latency(0x01)   | Gauge        | us    |      | 设备层时延最总计值              |
| latency_device_jitter | io_latency(0x01)   | Gauge        | us    |      | 设备层时延抖动                  |
| count_latency_device  | io_latency(0x01)   | Gauge        |       |      | 设备层操作次数                  |
| err_code              | io_err(0x02)       | Gauge        |       |      | block层I/O操作错误码            |
| read_bytes            | io_count(0x04)     | Gauge        | bytes |      | I/O操作读字节数                 |
| write_bytes           | io_count(0x04)     | Gauge        | bytes |      | I/O操作写字节数                 |
| access_pagecache      | io_pagecache(0x08) | Gauge        |       |      | Block页面访问次数               |
| mark_buffer_dirty     | io_pagecache(0x08) | Gauge        |       |      | Block   page buffer置脏次数     |
| load_page_cache       | io_pagecache(0x08) | Gauge        |       |      | Block   page 加入page cache次数 |
| mark_page_dirty       | io_pagecache(0x08) | Gauge        |       |      | Block   page 置脏次数           |

# Container

| metrics_name                           | table_name        | metrics_type | unit    | KPI  | metrics description                                          |
| -------------------------------------- | ----------------- | ------------ | ------- | ---- | ------------------------------------------------------------ |
| container_id                           | container         | key          |         |      | 容器ID（简写）                                               |
| name                                   | container         | label        |         |      | 容器名称                                                     |
| cpucg_inode                            | container         | label        |         |      | cpu,cpuacct   cgroup ID（容器实例内cgroup目录对应的inode id） |
| memcg_inode                            | container         | label        |         |      | memory   cgroup ID（容器实例内cgroup目录对应的inode id）     |
| pidcg_inode                            | container         | label        |         |      | pids   cgroup ID（容器实例内cgroup目录对应的inode id）       |
| mnt_ns_id                              | container         | label        |         |      | mount   namespace                                            |
| net_ns_id                              | container         | label        |         |      | net   namespace                                              |
| proc_id                                | container         | label        |         |      | 容器主进程ID                                                 |
| blkio_device_usage_total               | container_blkio   | Gauge        | bytes   |      | Blkio   device bytes usage, unit bytes                       |
| cpu_load_average_10s                   | container_cpu     | Gauge        |         |      | Value   of container cpu load average over the last 10 seconds |
| cpu_system_seconds_total               | container_cpu     | Gauge        | seconds | Y    | Cumulative   system cpu time consumed, unit second           |
| cpu_usage_seconds_total                | container_cpu     | Gauge        | seconds | Y    | Cumulative   cpu time consumed, unit second                  |
| cpu_user_seconds_total                 | container_cpu     | Gauge        | seconds |      | Cumulative   user cpu time consumed, unit second             |
| fs_inodes_free                         | container_fs      | Gauge        |         |      | Number   of available Inodes                                 |
| fs_inodes_total                        | container_fs      | Gauge        |         |      | Total   number of Inodes                                     |
| fs_io_current                          | container_fs      | Gauge        |         |      | Number   of I/Os currently in progress                       |
| fs_io_time_seconds_total               | container_fs      | Gauge        | seconds |      | Cumulative   count of seconds spent doing I/Os, unit second  |
| fs_io_time_weighted_seconds_total      | container_fs      | Gauge        | seconds |      | Cumulative   weighted I/O time, unit second                  |
| fs_limit_bytes                         | container_fs      | Gauge        | bytes   |      | Number   of bytes that can be consumed by the container on this filesystem, unit bytes |
| fs_read_seconds_total                  | container_fs      | Gauge        | bytes   |      | Cumulative   count of bytes read, unit bytes                 |
| fs_reads_bytes_total                   | container_fs      | Gauge        | bytes   |      | Cumulative count of bytes read                               |
| fs_reads_merged_total                  | container_fs      | Gauge        |         |      | Cumulative   count of reads merged                           |
| fs_reads_total                         | container_fs      | Gauge        |         |      | Cumulative   count of reads completed                        |
| fs_sector_reads_total                  | container_fs      | Gauge        |         |      | Cumulative   count of sector reads completed                 |
| fs_sector_writes_total                 | container_fs      | Gauge        |         |      | Cumulative   count of sector writes completed                |
| fs_usage_bytes                         | container_fs      | Gauge        | bytes   |      | Number   of bytes that are consumed by the container on this filesystem |
| fs_write_seconds_total                 | container_fs      | Gauge        | seconds |      | Cumulative   count of seconds spent writing                  |
| fs_writes_bytes_total                  | container_fs      | Gauge        | bytes   |      | Cumulative   count of bytes written                          |
| fs_writes_merged_total                 | container_fs      | Gauge        |         |      | Cumulative   count of writes merged                          |
| fs_writes_total                        | container_fs      | Gauge        |         |      | Cumulative   count of writes completed                       |
| memory_cache                           | container_memory  | Gauge        | bytes   | Y    | Total   page cache memory                                    |
| memory_failcnt                         | container_memory  | Gauge        |         |      | Number   of memory usage hits limits                         |
| memory_failures_total                  | container_memory  | Gauge        |         |      | Cumulative   count of memory allocation failures             |
| memory_mapped_file                     | container_memory  | Gauge        | bytes   |      | Size   of memory mapped files                                |
| memory_max_usage_bytes                 | container_memory  | Gauge        | bytes   |      | Maximum   memory usage recorded                              |
| memory_rss                             | container_memory  | Gauge        | bytes   | Y    | Size   of RSS                                                |
| memory_swap                            | container_memory  | Gauge        | bytes   | Y    | Container   swap usage                                       |
| memory_usage_bytes                     | container_memory  | Gauge        | bytes   | Y    | Current   memory usage, including all memory regardless of when it was accessed |
| memory_working_set_bytes               | container_memory  | Gauge        | bytes   |      | Current   working set                                        |
| network_receive_bytes_total            | container_network | Gauge        | bytes   |      | Cumulative   count of bytes received                         |
| network_receive_errors_total           | container_network | Gauge        |         |      | Cumulative   count of errors encountered while receiving     |
| network_receive_packets_dropped_total  | container_network | Gauge        |         |      | Cumulative   count of packets dropped while receiving        |
| network_receive_packets_total          | container_network | Gauge        |         | Y    | Cumulative   count of packets received                       |
| network_transmit_bytes_total           | container_network | Gauge        | bytes   | Y    | Cumulative   count of bytes transmitted                      |
| network_transmit_errors_total          | container_network | Gauge        |         |      | Cumulative   count of errors encountered while transmitting  |
| network_transmit_packets_dropped_total | container_network | Gauge        |         |      | Cumulative   count of packets dropped while transmitting     |
| network_transmit_packets_total         | container_network | Gauge        |         |      | Cumulative   count of packets transmitted                    |
| oom_events_total                       | container_oom     | Gauge        |         |      | Count   of out of memory events observed for the container   |
| spec_cpu_period                        | container_spec    | Gauge        |         |      | CPU   period of the container                                |
| spec_cpu_shares                        | container_spec    | Gauge        |         |      | CPU   share of the container                                 |
| spec_memory_limit_bytes                | container_spec    | Gauge        | bytes   |      | Memory   limit for the container                             |
| spec_memory_reservation_limit_bytes    | container_spec    | Gauge        | bytes   |      | Memory   reservation limit for the container                 |
| spec_memory_swap_limit_bytes           | container_spec    | Gauge        | bytes   |      | Memory   swap limit for the container                        |
| start_time_seconds                     | container_start   | Gauge        | seconds |      | Start   time of the container since unix epoch               |
| tasks_state                            | container_tasks   | Gauge        |         |      | Number   of tasks in given state (sleeping, running, stopped, uninterruptible, or   ioawaiting) |
|                                        |                   |              |         |      |                                                              |

# Redis SLI

| metrics_name | table_name    | metrics_type | unit | KPI  | metrics description              |
| ------------ | ------------- | ------------ | ---- | ---- | -------------------------------- |
| tgid         |               | key          |      |      | 进程ID                           |
| ins_id       |               | key          |      |      | 实例ID                           |
| app          |               | key          |      |      | 应用名                           |
| method       |               | key          |      |      | 请求方法                         |
| server_ip    |               | label        |      |      | 服务端IP                         |
| server_port  |               | label        |      |      | 服务端端口                       |
| client_ip    |               | label        |      |      | 客户端IP                         |
| client_port  |               | label        |      |      | 客户端端口                       |
| rtt_nsec     | redis_sli     | gauge        | ns   | Y    | Redis协议请求RTT                 |
| max_rtt_nsec | redis_max_sli | gauge        | ns   | Y    | Redis协议采样周期内最大请求RTT   |

# Postgre SLI

支持版本：openssl 1.1.1

| metrics_name | table_name | metrics_type | unit | KPI  | metrics description                   |
| ------------ | ---------- | ------------ | ---- | ---- | ------------------------------------- |
| tgid         |            | key          |      |      | 进程ID                                |
| ins_id       |            | key          |      |      | 实例ID                                |
| app          |            | key          |      |      | 应用名                                |
| method       |            | key          |      |      | 请求方法                              |
| server_ip    |            | label        |      |      | 服务端IP                              |
| server_port  |            | label        |      |      | 服务端端口                            |
| client_ip    |            | label        |      |      | 客户端IP                              |
| client_port  |            | label        |      |      | 客户端端口                            |
| rtt_nsec     | pg_sli     | gauge        | ns   | Y    | Postgre协议请求RTT                    |
| max_rtt_nsec | pg_max_sli | gauge        | ns   | Y    | Postgre协议采样周期内最大请求RTT      |
| tps          | pg_tps     | gauge        |      | Y    | 数据库吞吐量，目前只支持openGauss 2.0 |

# HTTP SLI

待补充支持的版本

| metrics_name | table_name   | metrics_type | unit | KPI  | metrics description            |
| ------------ | ------------ | ------------ | ---- | ---- | ------------------------------ |
| tgid         |              | key          |      |      | 进程ID                         |
| ins_id       |              | key          |      |      | 实例ID                         |
| app          |              | key          |      |      | 应用名                         |
| method       |              | key          |      |      | 请求方法                       |
| server_ip    |              | label        |      |      | 服务端IP                       |
| server_port  |              | label        |      |      | 服务端端口                     |
| client_ip    |              | label        |      |      | 客户端IP                       |
| client_port  |              | label        |      |      | 客户端端口                     |
| rtt_nsec     | http_sli     | gauge        | ns   |      | 单个HTTP请求RTT                |
| max_rtt_nsec | http_max_sli | gauge        | ns   |      | 当前TCP连接所有HTTP请求最长RTT |

# DISK

| metrics_name | table_name    | metrics_type | unit                  | KPI  | metrics description                     |
| ------------ | ------------- | ------------ | --------------------- | ---- | --------------------------------------- |
| disk_name    | system_iostat | key          |                       |      | blk所在的物理磁盘名称                   |
| rspeed       | system_iostat | gauge        | read   bytes/second   | Y    | 读速率（IOPS）                          |
| rspeed_kB    | system_iostat | gauge        | read   kbytes/second  | Y    | 吞吐量                                  |
| r_await      | system_iostat | gauge        | ms                    | Y    | 读响应时间                              |
| rareq        | system_iostat | gauge        |                       | Y    | 饱和度(rareq-sz   和 wareq-sz+响应时间) |
| wspeed       | system_iostat | gauge        | write   bytes/second  | Y    | 写速率（IOPS）                          |
| wspeed_kB    | system_iostat | gauge        | write   kbytes/second | Y    | 吞吐量                                  |
| w_await      | system_iostat | gauge        | ms                    | Y    | 写响应时间                              |
| wareq        | system_iostat | gauge        |                       |      | 饱和度(rareq-sz   和 wareq-sz+响应时间) |
| aqu          | system_iostat | gauge        |                       |      | 平均队列深度                            |
| util         | system_iostat | gauge        | %                     | Y    | 磁盘使用率                              |

# NIC

| metrics_name       | table_name | metrics_type | unit     | KPI  | metrics description    |
| ------------------ | ---------- | ------------ | -------- | ---- | ---------------------- |
| dev_name           | nic        | key          |          |      | 网卡名称               |
| rx_bytes           | nic        | gauge        | bytes    |      | 网卡接收字节数         |
| rx_packets         | nic        | gauge        |          |      | 网卡接收的总数据包数   |
| rx_errs            | nic        | gauge        |          |      | 网卡接收错误的数据包数 |
| rx_dropped         | nic        | gauge        |          |      | 网卡接收丢弃的数据包数 |
| tx_bytes           | nic        | gauge        | bytes    |      | 网卡发送字节数         |
| tx_packets         | nic        | gauge        |          |      | 网卡发送的总数据包数   |
| tx_errs            | nic        | gauge        |          |      | 网卡发送错误的数据包数 |
| tx_dropped         | nic        | gauge        |          |      | 网卡发送丢弃的数据包数 |
| rxspeed_KB         | nic        | gauge        | Kbytes/s |      | 网卡上行速率           |
| txspeed_KB         | nic        | gauge        | Kbytes/s |      | 网卡下行速率           |
| tc_sent_drop       | nic        | gauge        |          |      | TC发送丢包             |
| tc_sent_overlimits | nic        | gauge        |          |      | TC发送队列溢出         |
| tc_backlog         | nic        | gauge        |          |      | TC backlog队列包数量   |
| tc_ecn_mark        | nic        | gauge        |          |      | TC 拥塞标记            |

# CPU

| metrics_name         | table_name      | metrics_type | unit    | KPI  | metrics description           |
| -------------------- | --------------- | ------------ | ------- | ---- | ----------------------------- |
| cpu                  | system_cpu      | key          |         |      | CPU编号                        |
| rcu                  | system_cpu      | gauge        |         |      | RCU锁软中断次数                 |
| timer                | system_cpu      | gauge        |         |      | 定时器软中断次数                |
| sched                | system_cpu      | gauge        |         |      | 调度中断次数                    |
| net_rx               | system_cpu      | gauge        |  |      | 网卡收包中断次数                |
| user_msec | system_cpu      | gauge        | ms |      | 用户态cpu占用时间（不包括nice）  |
| nice_msec | system_cpu      | gauge        | ms |      | nice用户态cpu占用时间（低优先级） |
| system_msec | system_cpu      | gauge        | ms |      | 内核态cpu占用时间               |
| iowait_msec | system_cpu      | gauge        | ms |      | 等待I/O完成的时间               |
| irq_msec | system_cpu      | gauge        | ms |      | 硬中断时间                      |
| softirq_msec | system_cpu      | gauge        | ms |      | 软中断时间                      |
| backlog_drops        | system_cpu      | gauge        |         |      | softnet_data队列满而丢弃报文数量 |
| rps_count            | system_cpu      | gauge        |         |      | CPU收到的RPS次数                |
| total_used_per       | system_cpu_util | gauge        | %       |      | CPU总利用率                     |

# MEM

| metrics_name | table_name     | metrics_type | unit | KPI  | metrics description |
| ------------ | -------------- | ------------ | ---- | ---- | ------------------- |
| mem          | system_meminfo | key          |      |      | /proc/meminfo       |
| mem_total    | system_meminfo | gauge        | KB   |      | 系统总的可用物理内存   |
| mem_free     | system_meminfo | gauge        | KB   |      | 系统还可用的物理内存   |
| mem_available| system_meminfo | gauge        | KB   |      | 用户还可用内存        |
| mem_util     | system_meminfo | gauge        | %    |      | 系统内存使用率        |
| mem_buffers  | system_meminfo | gauge        | KB   |      | 被 buffer使用的物理内存|
| mem_cached   | system_meminfo | gauge        | KB   |      | 被 cache使用的物理内存 |
| mem_active   | system_meminfo | gauge        | KB   |      | 经常使用的cache页面大小|
| mem_inactive | system_meminfo | gauge        | KB   |      | 非活跃内存大小,可回收  |
| swap_total   | system_meminfo | gauge        | KB   |      | 交换区总量           |
| swap_free    | system_meminfo | gauge        | KB   |      | 空闲交换区总量        |
| swap_util    | system_meminfo | gauge        | %    |      | 交换区的使用率        |

# FS

| metrics_name | table_name | metrics_type | unit | KPI  | metrics description     |
| ------------ | ---------- | ------------ | ---- | ---- | ----------------------- |
| MountOn      | system_df  | key          |      |      | 文件系统的挂载点        |
| Fsname       | system_df  | label        |      |      | 文件系统名称            |
| Fstype       | system_df  | label        |      |      | 文件系统类型            |
| Inodes       | system_df  | label        |      |      | 分区内inode数量         |
| IUsed        | system_df  | gauge        |      | Y    | 分区内已使用的inode数量 |
| IFree        | system_df  | gauge        |      |      | 分区内空闲的inode数量   |
| IUsePer      | system_df  | gauge        | %    |      | 分区内已使用的inode占比 |
| Blocks       | system_df  | label        | KB   |      | 分区内Block数量         |
| Used         | system_df  | gauge        |      | Y    | 分区内已使用的Block数量 |
| Free         | system_df  | gauge        |      | Y    | 分区内空闲的Block数量   |
| UsePer       | system_df  | gauge        | %    | Y    | 分区内已使用的Block占比 |

# NET

| metrics_name      | table_name | metrics_type | unit | KPI  | metrics description |
| ----------------- | ---------- | ------------ | ---- | ---- | ------------------- |
| origin            |            | key          |      |      | /proc/dev/snmp      |
| tcp_curr_estab    | system_tcp | gauge        |      |      | 当前的TCP连接数     |
| tcp_in_segs       | system_tcp | gauge        | segs |      | TCP接收的分片数     |
| tcp_out_segs      | system_tcp | gauge        | segs |      | TCP发送的分片数     |
| tcp_retrans_segs  | system_tcp | gauge        | segs | Y    | TCP重传的分片数     |
| tcp_in_errs       | system_tcp | gauge        |      |      | TCP入包错误包数     |
| udp_indata_grams  | system_udp | gauge        | segs |      | UDP接收包量         |
| udp_outdata_grams | system_udp | gauge        | segs |      | UDP发送包量         |

# Host

| metrics_name | table_name | metrics_type | unit | KPI  | metrics description              |
| ------------ | ---------- | ------------ | ---- | ---- | -------------------------------- |
| os_version   |            | key          |      |      | 操作系统版本信息                 |
| hostname     | system_os  | label        |      |      | 主机名                           |
| kversion     | system_os  | label        |      |      | 内核版本信息                     |
| cpu_num      | system_os  | label        |      |      | CPU核数                          |
| memory_MB    | system_os  | label        |      |      | 内存总量(MB)                     |
| ip_addr      | system_os  | label        |      |      | 所有的IP地址                     |
| value        | system_os  | gauge        |      |      | 一个固定值作为metric，无实际意义 |

# Dnsmasq（entity_name：dnsmasq_link）

| metrics_name | table_name   | metrics_type | unit | KPI  | metrics description |
| ------------ | ------------ | ------------ | ---- | ---- | ------------------- |
| client_ip    | dnsmasq_link | key          |      |      | 客户端IP            |
| virtual_ip   | dnsmasq_link | key          |      |      | DNS服务器IP         |
| family       | dnsmasq_link | label        |      |      | 协议族              |
| link_count   | dnsmasq_link | gauge        |      |      | 连接数              |

# LVS（entity_name：ipvs_link）

支持的软件版本：>=EulerOS 2.9，且加载ipvs KO

| metrics_name | table_name | metrics_type | unit | KPI  | metrics description |
| ------------ | ---------- | ------------ | ---- | ---- | ------------------- |
| client_ip    | ipvs_link  | key          |      |      | 客户端IP            |
| virtual_ip   | ipvs_link  | key          |      |      | 虚拟IP              |
| local_ip     | ipvs_link  | key          |      |      | 本地IP              |
| server_ip    | ipvs_link  | key          |      |      | 真实的服务端IP      |
| server_port  | ipvs_link  | key          |      |      | 真实的服务端端口    |
| virtual_port | ipvs_link  | key          |      |      | 虚拟地址端口        |
| protocol     | ipvs_link  | label        |      |      | 协议类型            |
| link_count   | ipvs_link  | gauge        |      |      | 连接数              |

# Nginx（entity_name：nginx_link）

支持的软件版本：1.12.1

| metrics_name | table_name | metrics_type | unit | KPI  | metrics description |
| ------------ | ---------- | ------------ | ---- | ---- | ------------------- |
| client_ip    | nginx_link | key          |      |      | 客户端IP            |
| virtual_ip   | nginx_link | key          |      |      | 虚拟服务器IP        |
| server_ip    | nginx_link | key          |      |      | 真实服务端IP        |
| virtual_port | nginx_link | key          |      |      | 虚拟服务器端口      |
| server_port  | nginx_link | key          |      |      | 真实服务端端口      |
| is_l7        | nginx_link | label        |      |      | 1—七层LB / 0—四层LB |
| link_count   | nginx_link | gauge        |      |      | 连接数              |

# Haproxy（entity_name：haproxy_link）

支持的软件版本：2.5-dev0

| metrics_name | table_name   | metrics_type | unit | KPI  | metrics description |
| ------------ | ------------ | ------------ | ---- | ---- | ------------------- |
| client_ip    | haproxy_link | key          |      |      | 客户端IP            |
| virtual_ip   | haproxy_link | key          |      |      | 虚拟服务器IP        |
| server_ip    | haproxy_link | key          |      |      | 真实服务端IP        |
| virtual_port | haproxy_link | key          |      |      | 虚拟服务器端口      |
| server_port  | haproxy_link | key          |      |      | 真实服务端端口      |
| protocol     | haproxy_link | label        |      |      | 协议类型(TCP/HTTP)  |
| link_count   | haproxy_link | gauge        |      |      | 连接数              |

# JVM（entity_name：jvm）

| metrics_name                  | table_name   | metrics_type | unit  | KPI  | metrics description           |
| ----------------------------- | ------------ | ------------ | ----- | ---- | ----------------------------- |
| jvm_tpid                      | jvm          | key          |       |      | Java 虚拟机的进程ID            |
| pkg_name_main_class           | jvm          | label        |       |      | jar包名字和main方法所在类文件   |
| jvm_type                      | jvm          | label        |       |      | JVM 类型                      |
| jvm_version                   | jvm          | label        |       |      | JVM 版本                      |
| process_cpu_seconds_total     | jvm          | gauge        | ms    |      | 耗费CPU信息                   |
| process_start_time_seconds    | jvm          | gauge        | ms    |      | 进程起始时间                   |
| jvm_heap_memory_bytes_used    | jvm          | gauge        | bytes |      | 堆内存的使用情况               |
| jvm_heap_occuped_percent      | jvm          | gauge        | %     |      | 可用堆内存占最大可用空间的比值  |
| jvm_nonheap_memory_bytes_used | jvm          | gauge        | bytes |      | 非堆内存使用情况               |
| jvm_nonheap_occuped_perscent  | jvm          | gauge        | %     |      | 可用非堆内存占最大可用空间的比值 |
| jvm_buffer_pool_total_size    | jvm          | gauge        | bytes |      | JVM缓冲区的总容量              |
| jvm_buffer_pool_used_bytes    | jvm          | gauge        | bytes |      | JVM缓冲区的使用情况            |
| jvm_gc_total_seconds          | jvm          | gauge        | ms    |      | 所有GC回收器的总耗时           |
| jvm_gc_total_count            | jvm          | gauge        |       |      | 所有GC回收器的总次数           |
| jvm_gc_avg_usage              | jvm          | gauge        |       |      | GC回收器的平均使用率           |
| jvm_threads_current           | jvm          | gauge        |       |      | 并行线程数                    |
| jvm_threads_peak              | jvm          | gauge        |       |      | 并行线程峰值                  |
| jvm_threads_deadlocked        | jvm          | gauge        |       |      | 死锁线程数                    |

# kafka (entity_name: kafka)

| metrics_name                  | table_name   | metrics_type | unit  | KPI  | metrics description           |
| ----------------------------- | ------------ | ------------ | ----- | ---- | ----------------------------- |
| msg_type                      | kafkaprobe   | key          |       |      | 访问类型，producer或consumer                         |
| client_ip                     | kafkaprobe   | key          |       |      | 客户端IP                                             |
| client_port                   | kafkaprobe   | key          |       |      | 客户端port                                           |
| num                           | kafkaprobe   | gauge        |       |      | 在一次采样周期中producer发布或consumer消费的消息数量    |
| topic                         | kafkaprobe   | key          |       |      | 消息的topic                                          |
| server_ip                     | kafkaprobe   | key          |       |      | kafka server所在主机的网卡IP                         |
| server_port                   | kafkaprobe   | key          |       |      | kafka server所绑定的端口号                           |