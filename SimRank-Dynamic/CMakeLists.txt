cmake_minimum_required(VERSION 3.6)
project(APS)

set(CMAKE_CXX_STANDARD 11)

find_package(Boost REQUIRED serialization program_options)

set(DYNAMIC_SOURCE_FILES stat.cpp graph.cpp main_dynamic_exp.cpp)

set(SFMT_COMPILE_FLAGS -DSFMT_MEXP=607 -msse2 -DHAVE_SSE2)

macro(InitBoostHashSFMTEigen exec_name)
    target_include_directories(${exec_name} PRIVATE ${Boost_INCLUDE_DIRS})
    target_include_directories(${exec_name} PRIVATE ../dependencies/sparsehash-yche/include)
    target_include_directories(${exec_name} PRIVATE ../dependencies/sparsepp)
    target_include_directories(${exec_name} PRIVATE ../dependencies/SFMT)
    target_include_directories(${exec_name} PRIVATE ../dependencies/include)
endmacro()

################### dynamic exp
add_executable(dynamic-flp ${DYNAMIC_SOURCE_FILES} local_push.cpp local_push.h)
InitBoostHashSFMTEigen(dynamic-flp)
target_link_libraries(dynamic-flp PRIVATE ${Boost_LIBRARIES})
target_compile_options(dynamic-flp PRIVATE -O3 -g ${SFMT_COMPILE_FLAGS})
target_compile_definitions(dynamic-flp PRIVATE -DFLP=1)

############# dynamic-rlp
add_executable(dynamic-rlp ${DYNAMIC_SOURCE_FILES} local_push.cpp local_push.h)
InitBoostHashSFMTEigen(dynamic-rlp)
target_link_libraries(dynamic-rlp PRIVATE ${Boost_LIBRARIES})
target_compile_options(dynamic-rlp PRIVATE -O3 -g ${SFMT_COMPILE_FLAGS})



