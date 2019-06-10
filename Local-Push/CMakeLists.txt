cmake_minimum_required(VERSION 3.6)
project(pLP)

option(BUILD_PLAYGROUND "build playground" ON)

################################ 1st: basic environmental variables ######################################
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_EXPORT_COMPILE_COMMANDS "ON")

if (NOT DATA_ROOT_PATH) ### setup the data set root dir path
    message("not given")
endif ()
add_compile_definitions(DATA_ROOT="${DATA_ROOT_PATH}")
add_compile_definitions(USE_LOG)

message("Data Root Dir: ${DATA_ROOT_PATH}")
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH}) # To allow CMake to locate our Find*.cmake files

################################ 2nd: libarary environmental variables ######################################
find_package(Tcmalloc)
find_package(Boost REQUIRED program_options)
if (Boost_FOUND)
    message("Boost Include Dir: ${Boost_INCLUDE_DIRS}")
endif ()

################################ 3rd: source file environmental variables ######################################
set(UTIL_FILES util/stat.cpp util/graph_yche.cpp util/log.cpp)
set(GROUND_TRUTH_FILES yche_refactor/simrank.cpp)
set(LP_FILES yche_refactor/local_push_yche.cpp yche_refactor/local_push_yche_rlp.cpp)

############################## general macros ###################
macro(InitBoostHashSFMTEigen exec_name)
    target_include_directories(${exec_name} PRIVATE ${Boost_INCLUDE_DIRS})
    target_include_directories(${exec_name} PRIVATE ../dependencies/sparsehash-yche/include)
    target_include_directories(${exec_name} PRIVATE ../dependencies/sparsepp)
    target_include_directories(${exec_name} PRIVATE ../dependencies/include)
endmacro()

macro(InitFLP exec_name)
    InitBoostHashSFMTEigen(${exec_name})
    target_link_libraries(${exec_name} PRIVATE ${Boost_LIBRARIES})
    if (Tcmalloc_FOUND)
        target_link_libraries(${exec_name} PRIVATE ${Tcmalloc_LIBRARY})
    endif ()
    target_compile_options(${exec_name} PRIVATE -O3 -g -march=native)
    target_link_libraries(${exec_name} PRIVATE ${CMAKE_THREAD_LIBS_INIT})
    if (OPENMP_FOUND)
        target_compile_definitions(${exec_name} PRIVATE -DHAS_OPENMP=1)
    endif ()
endmacro()

####################### 4th: reduced local push testing
if (BUILD_PLAYGROUND)
    add_subdirectory(playground)
endif ()

add_executable(rlp playground/main_exec_reduced_local_push.cpp ${UTIL_FILES} ${GROUND_TRUTH_FILES} ${LP_FILES})
InitFLP(rlp)

add_executable(flp playground/main_exec_reduced_local_push.cpp ${UTIL_FILES} ${GROUND_TRUTH_FILES} ${LP_FILES})
InitFLP(flp)
target_compile_definitions(flp PRIVATE -DFLP=1)
