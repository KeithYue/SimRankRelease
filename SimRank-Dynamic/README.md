# Dynamic Update For FLP/RLP

## Build

```
mkdir -p build
cd build
cmake ..
make -j
```

## Config Dataset Root Dir Relative Path

```
ln -s /homes/ywangby/workspace/DynamicSimRank/datasets
```

## Usage

```
./dynamic-flp ca-GrQc 100 ins
./dynamic-flp ca-GrQc 100 del
./dynamic-rlp ca-GrQc 100 ins
./dynamic-rlp ca-GrQc 100 del
```

## Example Output of `./dynamic-rlp ca-GrQc 100 ins`

```
insertion exp
loading /homes/ywangby/workspace/LinsysSimRank/datasets/edge_list/ca-GrQc.txt
begin rlp constructing
begin generate edges...
begin dynamic update...
data: ca-GrQc
number of updates: 100
total time: 0.179428
avg time: 0.00179428
```