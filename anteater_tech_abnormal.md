# gala-anteater异常事件

## 简介

gala-anteater是一款基于AI的操作系统异常检测平台。主要涵盖时序数据预处理、异常点发现、以及异常上报等功能。其基于线下预训练、线上模型的增量学习与模型更新，能够很好地适应于多维多模态数据故障诊断。

## 指导手册集合
* 用户安装指导手册，请参考这里：[Link](https://gitee.com/openeuler/gala-anteater/blob/master/README.md)。
* 开发者指导手册，请参考这里：[Link](https://gitee.com/openeuler/gala-anteater/blob/master/docs/dev_introduction.md)。

## 支持的异常事件

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

gala-anteater应用级异常事件，主要基于应用级SLI指标进行异常检测，当前使用的sli指标为：
* gala_gopher_sli_rtt_nsec
* gala_gopher_sli_tps

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
* gala_gopher_tcp_link_syn_srtt

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
* gala_gopher_tcp_link_srtt

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
* gala_gopher_block_latency_req_max

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
* gala_gopher_proc_bio_latency
* gala_gopher_proc_less_4k_io_read
* gala_gopher_proc_less_4k_io_write
* gala_gopher_proc_greater_4k_io_read
* gala_gopher_proc_greater_4k_io_write

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
* gala_gopher_disk_r_await
* gala_gopher_disk_w_await

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