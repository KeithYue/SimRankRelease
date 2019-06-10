# FLP and RLP

## Dir Organization

util | files
--- | ---
graph(bidirectional csr) | [graph_yche.h](util/graph_yche.h), [graph_yche.cpp](util/graph_yche.cpp)
spare matrix | [sparse_matrix_utils.h](util/sparse_matrix_utils.h)
file serialization | [file_serialization.h](util/file_serialization.h)
profiling | [stat.h](util/stat.h), [stat.cpp](util/stat.cpp)

all pair algorithms | files
--- | ---
flp | [local_push_yche.h](yche_refactor/local_push_yche.h), [local_push_yche.cpp](yche_refactor/local_push_yche.cpp)
rlp | [local_push_yche_rlp.cpp](yche_refactor/local_push_yche_rlp.cpp)
iterative(ground truth) | [simrank.h](yche_refactor/simrank.h), [simrank.cpp](yche_refactor/simrank.cpp)

## Dependencies

lib | comment
--- | ---
boost | for `program_options` and some header-only functions
eigen | see [../dependencies/include](../dependencies/include)
sparsepp | see [../dependencies/sparsepp](../dependencies/sparsepp)

## Build

def | comment
--- | ---
`BUILD_PLAYGROUND` | an option to build playgrounds
`EXTRA_INCLUDE_DIR` | tcmalloc
`DATA_ROOT_PATH` | input/output dataset root folder

```zsh
mkdir -p build
cd build
cmake .. -DBUILD_PLAYGROUND=OFF -DEXTRA_INCLUDE_DIR=/homes/ywangby/workspace/yche/local/include -DDATA_ROOT_PATH=/homes/ywangby/workspace/DynamicSimRank/datasets
make -j
```

## Usage

```zsh
./rlp ca-GrQc 0.01
./flp ca-GrQc 0.01
```

## Example Output of `./rlp ca-GrQc 0.01`

* w/o ground truth to compute it from scratch

```
/homes/ywangby/workspace/DynamicSimRank/datasets/bin_edge_list/ca-GrQc.bin
load edge list time:55 ms
parse edge list to bi-dir csr time:0 ms
data path /homes/ywangby/workspace/DynamicSimRank/datasets/local_push/RLP_ca-GrQc-0.600-0.010000.P
file not exists, compute from scratch
computation time: 0.751199 s
push num: 18857896
max queue size #: 317
mem size: 21624 KB
P size: 108335
R size: 766617
avg push merge #: 24
/homes/ywangby/workspace/DynamicSimRank/datasets/ground_truth/GROUND_ca-GrQc-0.600.bin
max iterations: 100
max err:0.00999999
mean err:0.000121508
```

* with already generated ground truth

```
/homes/ywangby/workspace/DynamicSimRank/datasets/bin_edge_list/ca-GrQc.bin
load edge list time:74 ms
parse edge list to bi-dir csr time:0 ms
data path /homes/ywangby/workspace/DynamicSimRank/datasets/local_push/RLP_ca-GrQc-0.600-0.010000.P
file not exists, compute from scratch
computation time: 0.660413 s
push num: 18857896
max queue size #: 317
mem size: 21304 KB
P size: 108335
R size: 766617
avg push merge #: 24
/homes/ywangby/workspace/DynamicSimRank/datasets/ground_truth/GROUND_ca-GrQc-0.600.bin
loading ground truth....
max err:0.00999999
mean err:0.000121508
```
