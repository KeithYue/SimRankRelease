cmake_minimum_required(VERSION 3.6)
project(LPMC-Optimizing)

################################ 1st: basic environmental variables ######################################
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_EXPORT_COMPILE_COMMANDS "ON")
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH}) # To allow CMake to locate our Find*.cmake files

if (NOT DATA_ROOT_PATH) ### setup the data set root dir path
    message("not given")
endif ()
add_compile_definitions(DATA_ROOT="${DATA_ROOT_PATH}")

################################ 2nd: libarary environmental variables ######################################
find_package(Boost REQUIRED program_options)
if (Boost_FOUND)
    message(${Boost_INCLUDE_DIRS})
endif ()
find_package(OpenMP)
if (OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
endif ()

################################ 3rd: source file environmental variables ######################################
set(UTIL_FILES util/stat.cpp ../dependencies/SFMT/SFMT.c util/graph_yche.cpp)
set(BIN_SEARCH_FIES util/search_yche.cpp)
set(GROUND_TRUTH_FILES yche_refactor/simrank.cpp)
set(SFMT_COMPILE_FLAGS -DSFMT_MEXP=607 -msse2 -DHAVE_SSE2)

set(BPRW_FILES yche_refactor/bprw_yche.cpp ${BIN_SEARCH_FIES})
set(LPMC_FILES yche_refactor/flpmc_yche.cpp
        yche_refactor/local_push_yche.cpp
        yche_refactor/parallel_local_push/parallel_local_push_yche.cpp
        yche_refactor/parallel_local_push/parallel_local_push_yche_rlp.cpp)

############################## general macros ###################
macro(InitBoostHashSFMTEigen exec_name)
    target_include_directories(${exec_name} PRIVATE ${Boost_INCLUDE_DIRS})
    target_include_directories(${exec_name} PRIVATE ../dependencies/sparsehash-yche/include)
    target_include_directories(${exec_name} PRIVATE ../dependencies/sparsepp)
    target_include_directories(${exec_name} PRIVATE ../dependencies/SFMT)
    target_include_directories(${exec_name} PRIVATE ../dependencies/include)
    target_compile_definitions(${exec_name} PRIVATE -DHAS_OPENMP=1)
endmacro()

include(FindSSE)
if (AVX2_FOUND)
    message("found AVX2")
    add_compile_options(-mavx2)
elseif (AVX_FOUND)
    message("found AVX")
    add_compile_options(-mavx)
elseif (SSE4_1_FOUND)
    message("found sse4.1")
    add_compile_options(-msse4.1)
elseif (SSE2_FOUND)
    message("found sse2")
    add_compile_options(-msse2)
endif ()

#### sub cmake dir: playground  ######################################
add_subdirectory(util)

#### 1st: bprw #######################################################################################################
macro(InitBprw exec_name)
    InitBoostHashSFMTEigen(${exec_name})
    target_link_libraries(${exec_name} PRIVATE tcmalloc)
    target_compile_options(${exec_name} PRIVATE -O3 -g ${SFMT_COMPILE_FLAGS} -march=native)
endmacro()

set(BLPMC_SOURCE_FILES ${BPRW_FILES} ${UTIL_FILES} ${GROUND_TRUTH_FILES} playground/main_bprw.cpp)
add_executable(bprw ${BLPMC_SOURCE_FILES})
InitBprw(bprw)
target_compile_definitions(bprw PRIVATE -DSFMT=1)

add_executable(bprw-std ${BLPMC_SOURCE_FILES})
InitBprw(bprw-std)

#### 2nd: flpmc #######################################################################################################
macro(InitFLPMC exec_name)
    InitBoostHashSFMTEigen(${exec_name})
    message(${Boost_LIBRARIES})
    target_link_libraries(${exec_name} PRIVATE ${Boost_LIBRARIES})
    target_link_libraries(${exec_name} PRIVATE tcmalloc)
    target_compile_options(${exec_name} PRIVATE -O3 -g ${SFMT_COMPILE_FLAGS} -march=native)
endmacro()
set(FLPMC_SOURCE_FILES ${LPMC_FILES} ${UTIL_FILES} ${GROUND_TRUTH_FILES} playground/main_flpmc.cpp)

add_executable(flpmc ${FLPMC_SOURCE_FILES})
InitFLPMC(flpmc)
target_compile_definitions(flpmc PRIVATE -DSFMT=1)

#### 3rd: bflpmc #######################################################################################################
macro(InitBFLPMC exec_name)
    InitFLPMC(${exec_name})
endmacro()
set(BFLPMC_SOURCE_FILES ${LPMC_FILES} ${BPRW_FILES} ${UTIL_FILES} ${GROUND_TRUTH_FILES}
       yche_refactor/bflpmc_yche.cpp playground/main_bflpmc.cpp)
add_executable(bflpmc ${BFLPMC_SOURCE_FILES})
InitBFLPMC(bflpmc)
target_compile_definitions(bflpmc PRIVATE -DSFMT=1)
