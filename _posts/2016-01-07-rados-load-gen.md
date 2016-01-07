---
title: Ceph Rados的测试：load-gen
tags: ceph
---

对Ceph的性能测试，常见的有fio测试rbd，而对rados的直接测试，可以通过`rados bench`命令，本篇介绍另外一个命令`rados load-gen`

<!--more-->

整体流程是这样的

```
1. LoadGen lg(&rados);      // 实例化测试类
2. lg.bootstrap(pool_name); // 生成初始的测试对象
    生成指定个数和随机大小的测试对象，然后写入pool，实际只会在len位置写入1B（例如：需要生成1024B的对象，实际会在off=1024的位置写1B，相当于占了1024的空间），应该是为了快速生成，但这样的话就不能测试EC pool，可以通过修改源码来支持。
3. lg.run();                // 测试过程
    循环生成op提交
4. lg.cleanup();            // 清理生成的对象
```

再来看参数设置

```sh
--num-objects           初始生成测试用的对象数，默认 200
--min-object-size       测试对象的最小大小，默认 1KB，单位byte 
--max-object-size       测试对象的最大大小，默认 5GB，单位byte

--min-op-len            压测IO的最小大小，默认 1KB，单位byte
--max-op-len            压测IO的最大大小，默认 2MB，单位byte
--max-ops               一次提交的最大IO数，相当于iodepth
--target-throughput     一次提交IO的历史累计吞吐量上限，默认 5MB/s，单位B/s
--max-backlog           一次提交IO的吞吐量上限，默认10MB/s，单位B/s
--read-percent          读写混合中读的比例，默认80，范围[0, 100]

--run-length            运行的时间，默认60s，单位秒
```

一个典型的用例

```sh
# 测试4KB写，iodepth=64，速度不限
rados -p rbd load-gen --num-objects 128 --min-object-size 8192 --max-object-size 8192 --run-length 20 --read-percent 0 --min-op-len 4096 --max-op-len 4096 --target-throughput 104857600 --max_backlog 104857600 --max-ops 64
```

整体的流程和rados bench差不多，但是支持跟丰富的测试语义。