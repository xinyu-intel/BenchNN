# BenchNN

## NCF

### Build NCF Environment

```
docker build -f Dockerfile.NCF -t ncf:build
```

### Benchmark NCF
```
# benchmark fp32(optimized) one instance per core
docker run  --security-opt seccomp=seccomp.json -it --rm ncf:build  /work/ncf/benchmark.sh -p model/ml-20m/neumf-opt

# benchmark int8 one instance per core
docker run  --security-opt seccomp=seccomp.json -it --rm ncf:build  /work/ncf/benchmark.sh -p model/ml-20m/neumf-opt-quantized

# try different configurations
export NUM_SOCKET=`lscpu | grep 'Socket(s)' | awk '{print $NF}'`
export NUM_NUMA_NODE=`lscpu | grep 'NUMA node(s)' | awk '{print $NF}'`
export CORES_PER_SOCKET=`lscpu | grep 'Core(s) per socket' | awk '{print $NF}'`
export NUM_CORES=$((CORES_PER_SOCKET * NUM_SOCKET))
export CORES_PER_NUMA=$((NUM_CORES / NUM_NUMA_NODE))

# benchmark one instance per socket
docker run  --security-opt seccomp=seccomp.json -it --rm ncf:build  /work/ncf/benchmark.sh -p model/ml-20m/neumf-opt-quantized -i $NUM_SOCKET -c $CORES_PER_SOCKET
# benchmark one instance per numa
docker run  --security-opt seccomp=seccomp.json -it --rm ncf:build  /work/ncf/benchmark.sh -p model/ml-20m/neumf-opt-quantized -i $NUM_NUMA_NODE -c $CORES_PER_NUMA
# benchmark one instance per system
docker run  --security-opt seccomp=seccomp.json -it --rm ncf:build  /work/ncf/benchmark.sh -p model/ml-20m/neumf-opt-quantized -i 1 -c $NUM_CORES

# benchmark with different batchsize to get the best performance (default is 700)
docker run  --security-opt seccomp=seccomp.json -it --rm ncf:build  /work/ncf/benchmark.sh -p model/ml-20m/neumf-opt-quantized -b 700
```
