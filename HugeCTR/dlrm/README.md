

# NVIDIA HugeCTR DLRM Benchmark Test 

This folder holds NVIDIA HugeCTR DLRM Benchmark Test scripts, tools and reports.

You can refer to [HugeCTR User Guide](https://github.com/NVIDIA/HugeCTR/blob/master/docs/hugectr_user_guide.md) for additional information.

## folder structure
## Benchmark Test Cases

This report summarized HugeCTR test on 1 nodes with 8 x Tesla V100 on Dec 2021

### Test Environment
- 1 nodes with Tesla V100-SXM2-16GB x 8
- InfiniBand 100 Gb/sec (4X EDR)， Mellanox Technologies MT27700 Family
- Intel(R) Xeon(R) Gold 6271C CPU @ 2.60GHz  ($ cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c*)
- Memory 384G ($ cat /proc/meminfo)
- Ubuntu 20.04.3 LTS  ($  cat /etc/issue/) (GNU/Linux 5.4.0-26-generic x86_64)   ($  uname -a)
- CUDA Version: 11.4  ($  nvcc -V), Driver Version: 470.57.02  ($  cat /proc/driver/nvidia/version)
- HugeCTR version: 3.2
- `nvidia-smi topo -m`

```
		GPU0	GPU1	GPU2	GPU3	GPU4	GPU5	GPU6	GPU7	mlx5_0	mlx5_1	CPU Affinity    NUMA Affinity
GPU0	 X 		NV1	    NV2	    NV1	 	SYS		SYS		SYS		NV2		NODE	SYS		0-23,48-71		0
GPU1	NV1	 	X 		NV1		NV2		SYS		SYS		NV2		SYS		NODE	SYS		0-23,48-71		0
GPU2	NV2		NV1	 	X 		NV2		SYS		NV1		SYS		SYS		PIX		SYS		0-23,48-71		0
GPU3	NV1		NV2		NV2		X 		NV1		SYS		SYS		SYS		PIX		SYS		0-23,48-71		0
GPU4	SYS		SYS		SYS		NV1		X 		NV2		NV2		NV1		SYS		NODE	24-47,72-95		1
GPU5	SYS		SYS		NV1		SYS		NV2	 	X 		NV1		NV2		SYS		NODE	24-47,72-95		1
GPU6	SYS		NV2		SYS		SYS		NV2		NV1	 	X 		NV1		SYS		PIX		24-47,72-95		1
GPU7	NV2		SYS		SYS		SYS		NV1		NV2		NV1	 	X 		SYS		PIX		24-47,72-95		1
mlx5_0	NODE	NODE	PIX		PIX		SYS		SYS		SYS		SYS	 	X 		SYS		
mlx5_1	SYS		SYS		SYS		SYS		NODE	NODE	PIX		PIX		SYS	 	X 		

Legend:

  X    = Self
  SYS  = Connection traversing PCIe as well as the SMP interconnect between NUMA nodes (e.g., QPI/UPI)
  NODE = Connection traversing PCIe as well as the interconnect between PCIe Host Bridges within a NUMA node
  PHB  = Connection traversing PCIe as well as a PCIe Host Bridge (typically the CPU)
  PXB  = Connection traversing multiple PCIe bridges (without traversing the PCIe Host Bridge)
  PIX  = Connection traversing at most a single PCIe bridge
  NV#  = Connection traversing a bonded set of # NVLinks
```



### baseline 

command: bash dlrm.sh

| parameter                    | value                          |
| ---------------------------- | ------------------------------ |
| gpu_num_per_node             | 8                              |
| num_nodes                    | 1                              |
| eval_batchs                  | 70                             |
| batch_size                   | 65536                          |
| learning_rate                | 0.5                            |
| warmup_steps                 | 1000                           |
| data_dir                     | /dataset/f9f659c5/hugectr_dlrm |
| workspace_size_per_gpu_in_mb | 11645                          |
| embedding_vec_size           | 128                            |
| max_iter                     | 12000                          |
| loss_print_every_n_iter      | 100                            |
| eval_interval                | 100                            |
| eval_batch_size              | 65536                          |
| decay_start                  | 0                              |
| decay_steps                  | 1                              |
| decay_power                  | 2                              |
| end_lr',                     | 0                              |

### baseline 运行log

见baseline_log_info.csv

### Test Case

#### embedding size

one GPU

| gpu  | batch_size | embedding_vec_size | latency(ms) | memory_usage(MB) |
| ---- | ---------- | ------------------ | ----------- | ---------------- |
| n1g1 | 32768      | 2                  | 51.235      | 4,256            |
| n1g1 | 32768      | 8                  | 51.501      | 5,288            |
| n1g1 | 32768      | 32                 | 53.238      | 9,428            |
| n1g1 | 32768      | 128                | 65.268      | 25,968           |

eight GPUs

| gpu  | batch_size | embedding_vec_size | latency(ms) | memory_usage(MB) |
| ---- | ---------- | ------------------ | ----------- | ---------------- |
| n1g8 | 32768      | 2                  | 106.352     | 2,112            |
| n1g8 | 32768      | 8                  | 112.477     | 2,180            |
| n1g8 | 32768      | 32                 | 102.875     | 2,452            |
| n1g8 | 32768      | 128                | 295.483     | 3,540            |



#### batch size

one GPU

| gpu  | batch_size | embedding_vec_size | latency(ms) | memory_usage(MB) |
| ---- | ---------- | ------------------ | ----------- | ---------------- |
| n1g1 | 16         | 128                | 0.535       | 17,890           |
| n1g1 | 64         | 128                | 0.626       | 17,910           |
| n1g1 | 256        | 128                | 0.980       | 17,942           |
| n1g1 | 1024       | 128                | 2.219       | 18,138           |
| n1g1 | 4096       | 128                | 7.236       | 18,900           |
| n1g1 | 16384      | 128                | 27.148      | 21,930           |
| n1g1 | 32768      | 128                | 53.337      | 25,966           |

eight GPUs

| gpu  | batch_size | embedding_vec_size | latency(ms) | memory_usage(MB) |
| ---- | ---------- | ------------------ | ----------- | ---------------- |
| n1g8 | 16         | 128                | 0.889       | 1,670            |
| n1g8 | 64         | 128                | 1.161       | 1,682            |
| n1g8 | 256        | 128                | 2.333       | 1,700            |
| n1g8 | 1024       | 128                | 7.667       | 1,738            |
| n1g8 | 4096       | 128                | 33.855      | 1,910            |
| n1g8 | 16384      | 128                | 119.610     | 2,606            |
| n1g8 | 32768      | 128                | 211.966     | 3,540            |
| n1g8 | 65536      | 128                | 535.517     | 5,408            |



