# Filecoin 搭建

本文旨在避坑，本人作为矿工时期会保持更新。

## 1. 硬件与存储配置

需要的机器整体分为三种，包括主节点lotus（同步主网）、矿工节点miner（）

**管理节点(node200)**

- CPU Intel 4110R x 2

- 内存128G

- 无限卡

- 128G系统盘（M.2）

**主节点与矿工节点(node201)**

- CPU Intel 4110R x 2

- 内存256G

- 无显卡

**工作节点1(node202)**

- CPU Intel 4110 x 2

- 内存372G

**工作节点2(node203)**

- CPU AMD 7302 x 2

- Driver: NVIDIA-Linux-x86_64-460.91.03.run

## 2. 编译

我们需要根据不同的CPU型号来编译不同的二进制文件，源码文件建议放在共享目录中，如果机器数量足够多二进制文件也需要单独存放减少编译负担。

    #!/bin/bash
    set -x
    VERSION=v1.11.1
    source /root/env-lotus
    source /root/env-proxy
    #curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    #wget -c https://golang.org/dl/go1.16.7.linux-amd64.tar.gz -O - | sudo tar -xz -C /usr/local
    #echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.bashrc && source ~/.bashrc

    cd /filecoin/cache/build/lotus
    make clean
    git checkout master
    git pull
    git checkout $VERSION
    git submodule deinit --all
    git submodule update --init
    # Some older Intel and AMD processors WITHOUT the ADX instruction support may panic with illegal instruction errors.
    LSCPU=$(lscpu|grep -i adx)
    if [[ $? == 0 ]]
    then
        echo "Instruction ADX detected."
    else
        export CGO_CFLAGS_ALLOW="-D__BLST_PORTABLE__"
        export CGO_CFLAGS="-D__BLST_PORTABLE__"
    fi
    # If you have an AMD Zen or Intel Ice Lake CPU (or later), ENABLE the use of SHA extensions by adding these two environment variables:
    LSCPU=$(lscpu|grep -i ' sha')
    if [[ $? == 0 ]]
    then
        echo "Instruction SHA detected."
        export RUSTFLAGS="-C target-cpu=native -g"
        export FFI_BUILD_FROM_SOURCE=1
    fi
    make -j16
    make lotus-bench
    make install
    export https_proxy=


## 3. 分角色配置

**主节点与矿工节点(node201)**

这里我们的主节点与矿工节点使用同一台主机，其环境变量配置如下。

    #!/bin/bash
    export BELLMAN_CPU_UTILIZATION=0.875
    export FIL_PROOFS_MAXIMIZE_CACHING=1
    export FIL_PROOFS_USE_GPU_COLUMN_BUILDER=0
    export FIL_PROOFS_USE_GPU_TREE_BUILDER=0
    export FIL_PROOFS_USE_MULTICORE_SDR=1
    export FIL_PROOFS_SDR_PARENTS_CACHE_SIZE=1073741824
    #export RUST_BACKTRACE=full
    #export RUST_LOG=debug

    export FULLNODE_API_INFO="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBbGxvdyI6WyJyZWFkIiwid3JpdGUiLCJzaWduIiwiYWRtaW4iXX0.Wq0nVk1xEpwsrQhfxpk2Vb5lBS07NeJ6o4ZJRGoQuic:/ip4/192.168.0.101/tcp/1234/http"
    export MINER_API_INFO="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBbGxvdyI6WyJyZWFkIiwid3JpdGUiLCJzaWduIiwiYWRtaW4iXX0.c6SQus7UjC4rh-OKhi45f3RGr9UyH8jURrDsA521ZQ8:/ip4/192.168.0.101/tcp/2345/http"

    export LOTUS_PATH=/filecoin/data/node/ # When using a local node.
    export LOTUS_MINER_PATH=/filecoin/data/miner/
    export LOTUS_WORKER_PATH=/filecoin/data/worker/

    export FIL_PROOFS_PARAMETER_CACHE=/filecoin/cache/parameter/
    export FIL_PROOFS_PARENT_CACHE=/filecoin/cache/parent/
    export TMPDIR=/filecoin/cache/tmp/


    export IPFS_GATEWAY=https://proof-parameters.s3.cn-south-1.jdcloud-oss.com/ipfs/
    export GOPROXY=https://goproxy.cn
## 3. 测试

    编译完成后，需要对机器的能力进行简单测试，防止某些配置情况导致

## 3. 过程控制

虽然lotus的调度过程非常的傻，网上也有不少的文章都说对lotus的封装调度进行了优化，但是它一般是建立在机器数量较多的前提下，对于数量较少单个机器同时承载多个角色的情况，我们可以观察每个扇区在不同过程的表现来进行适当控制。

每个扇区在不同过程的消耗可以参考[Task resource table](https://docs.filecoin.io/mine/lotus/seal-workers/#resource-allocation-in-lotus-workers)。封装整体分为PreCommit与Commit两个过程，每个过程中又有两个不同阶段。其中，P1扇区可以并发，P2可以并发但会独占某个GPU，C1过程很快且可以并发，C2过程独占某个GPU且会排斥除AP外的其他任何新增阶段。

以扇区作为sectorsAgent，机器作为workerAgent，那么我们先定义sectorAgent的行为（为方便计算，假设每个扇区封装过程中占用空间为500G，不同的lotus版本、机器配置扇区封装过程表现的现象可能不同）。

    sectorAgent:
    AP -> P1(6h, 60G MEM) -> P2(2h, 20G MEM, GPU, Parallel) -> PreCommitAggregate -> C1(0.5h, 1G MEM) -> C2(1-2h, 200G MEM, only Parallel with GPU) -> SubmitCommitAggregate -> Submit

    workerAgent:
    Worker1(CPU only, 376G MEM)
    Worker2(wtih GPU, 1T MEM)