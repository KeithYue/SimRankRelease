## Dir Organization

util | files
--- | ---
graph(bidirectional csr) | [graph_yche.h](util/graph_yche.h), [graph_yche.cpp](util/graph_yche.cpp)
spare matrix | [sparse_matrix_utils.h](util/sparse_matrix_utils.h)
sfmt(simd fast rand gen) | [sfmt_based_rand.h](util/sfmt_based_rand.h)
binary/galloping search | [search_yche.h](util/search_yche.h), [search_yche.cpp](util/search_yche.cpp)
file serialization | [file_serialization.h](util/file_serialization.h)
profiling | [stat.h](util/stat.h), [stat.cpp](util/stat.cpp)

single-pair algorithm | files
--- | ---
bprw | [bprw_yche.h](yche_refactor/bprw_yche.h), [bprw_yche.cpp](yche_refactor/bprw_yche.cpp)
flpmc | [flpmc_yche.h](yche_refactor/flpmc_yche.h), [flpmc_yche.cpp](yche_refactor/flpmc_yche.cpp)
bflpmc | [bflpmc_yche.h](yche_refactor/bflpmc_yche.h), [bflpmc_yche.cpp](yche_refactor/bflpmc_yche.cpp)

all pair algorithms | files
--- | ---
local push(static) | [local_push_yche.h](yche_refactor/local_push_yche.h), [local_push_yche.cpp](yche_refactor/local_push_yche.cpp)
iterative(ground truth) | [simrank.h](yche_refactor/simrank.h), [simrank.cpp](yche_refactor/simrank.cpp)

## Build

```zsh
mkdir build
cd build
cmake .. -DDATA_ROOT_PATH=/homes/ywangby/workspace/DynamicSimRank/datasets
make -j
```

## Usage

single pair query

```zsh
./bprw ca-GrQc 0 1
./bprw-std ca-GrQc 0 1
./flpmc -d ca-GrQc -x 0 -y 1 -c 0.6
./bflpmc ca-GrQc 0 1
```

## Example of `./flpmc -d ca-GrQc -x 0 -y 1 -c 0.6`

* first create the local push folder

```
mkdir -p /homes/ywangby/workspace/DynamicSimRank/datasets/local_push/
```

* without index

```
load edge list time:53 ms
parse edge list to bi-dir csr time:0 ms
r_max: 0.0464159
data path /homes/ywangby/workspace/DynamicSimRank/datasets/local_push/FLP_ca-GrQc-0.600-0.116040.P
file not exists, compute from scratch
data path !!!!/homes/ywangby/workspace/DynamicSimRank/datasets/local_push/FLP_ca-GrQc-0.600-0.116040.P
local push offline index doesn't exists..
total indexing cost: 0.0731524 s
building compete, saving to /homes/ywangby/workspace/DynamicSimRank/datasets/local_push/FLP_ca-GrQc-0.600-0.116040
save complete
saved.
/homes/ywangby/workspace/DynamicSimRank/datasets/ground_truth/GROUND_ca-GrQc-0.600.bin
loading ground truth....
failure count:0
max err:0.00653624
min err:1.62998e-07

```

* with index

```
load edge list time:53 ms
parse edge list to bi-dir csr time:0 ms
r_max: 0.0464159
data path /homes/ywangby/workspace/DynamicSimRank/datasets/local_push/FLP_ca-GrQc-0.600-0.116040.P
data path !!!!/homes/ywangby/workspace/DynamicSimRank/datasets/local_push/FLP_ca-GrQc-0.600-0.116040.P
offline index exists..loading
/homes/ywangby/workspace/DynamicSimRank/datasets/ground_truth/GROUND_ca-GrQc-0.600.bin
loading ground truth....
failure count:0
max err:0.00653624
min err:1.62998e-07
```