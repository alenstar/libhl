cmake_minimum_required(VERSION 2.8)
project(hl C)

if("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_BINARY_DIR}")
    message(FATAL_ERROR "Do not build in-source.\nPlease remove CMakeCache.txt and the CMakeFiles/ directory.\nThen: mkdir _build ; cd _build ; cmake .. ; make")
endif()

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release" CACHE STRING
            "Choose the type of build, options are: Debug Profile Release Asan Ubsan." FORCE)
endif(NOT CMAKE_BUILD_TYPE)


if (${CMAKE_SYSTEM_NAME} MATCHES "Windows")
    set(WINDOWS TRUE)
    message(WARNING "only for linux")
else()
    set(LINUX TRUE)
endif()
if(BUILD_FOR_ARM)
    SET(CROSS_TOOLS_PATH /opt/crosstools/arm-2009q3/bin/arm-none-linux-gnueabi)
    SET(CROSS_ROOT_PATH /opt/crosstools/sys-root)

    # this one is important
    SET(CMAKE_SYSTEM_NAME Linux)
    #this one not so much
    SET(CMAKE_SYSTEM_VERSION 1)

    # specify the cross compiler
    SET(CMAKE_C_COMPILER   ${CROSS_TOOLS_PATH}-gcc)
    SET(CMAKE_CXX_COMPILER ${CROSS_TOOLS_PATH}-g++)

    # where is the target environment
    SET(CMAKE_FIND_ROOT_PATH  ${CROSS_ROOT_PATH})

    # search for programs in the build host directories
    SET(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
    # for libraries and headers in the target directories
    SET(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
    SET(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
endif()

#option(BUILD_EXAMPLE "Build example" ON)

#set(CMAKE_CXX_STANDARD 98)
set(CMAKE_C_STANDARD 99)

if(CMAKE_COMPILER_IS_GNUCC)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -std=gnu89")
endif()

add_definitions(-DUSING_PTHREAD)

configure_file(${CMAKE_SOURCE_DIR}/build/config.h ${CMAKE_BINARY_DIR}/config.h COPYONLY)

# add local library
#configure_file(${CMAKE_SOURCE_DIR}/libsqlite3.so ${CMAKE_BINARY_DIR}/libsqlite3.so COPYONLY)
#add_library(sqlite3 SHARED IMPORTED) # or STATIC instead of SHARED
#set_target_properties(sqlite3 PROPERTIES
#        IMPORTED_LOCATION "${USER_LIB_PATH}/libsqlite3.so"
#        INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_SOURCE_DIR}/include")
#target_link_libraries(<TARGET> sqlite3)

include_directories(include src ${CMAKE_SOURCE_DIR} ${CMAKE_BINARY_DIR})

set(LIBHL_SRCS
        src/atomic_defs.h
        src/avltree.c
        src/avltree.h
        src/binheap.c
        src/binheap.h
        src/bsd_queue.h
        src/comparators.h
        src/fbuf.c
        src/fbuf.h
        src/graph.c
        src/graph.h
        src/hashtable.c
        src/hashtable.h
        src/linklist.c
        src/linklist.h
        src/pqueue.c
        src/pqueue.h
        src/queue.c
        src/queue.h
        src/rbtree.c
        src/rbtree.h
        src/rbuf.c
        src/rbuf.h
        src/refcnt.c
        src/refcnt.h
        src/rqueue.c
        src/rqueue.h
        src/skiplist.c
        src/skiplist.h
        src/trie.c
        src/trie.h
        )

add_library("${PROJECT_NAME}_static" STATIC ${LIBHL_SRCS})

#if(BUILD_EXAMPLE)
#add_executable(queue_test test/queue_test.c)
#target_link_libraries(queue_test "${PROJECT_NAME}_static" pthread)
#endif()


install(FILES ${PROJECT_SOURCE_DIR}/src/queue.h DESTINATION ${CMAKE_BINARY_DIR}/include)
install(FILES ${PROJECT_SOURCE_DIR}/src/rqueue.h DESTINATION ${CMAKE_BINARY_DIR}/include)
install(FILES ${PROJECT_SOURCE_DIR}/src/pqueue.h DESTINATION ${CMAKE_BINARY_DIR}/include)
install(FILES ${PROJECT_SOURCE_DIR}/src/linklist.h DESTINATION ${CMAKE_BINARY_DIR}/include)
install(TARGETS "${PROJECT_NAME}_static" DESTINATION ${CMAKE_BINARY_DIR}/lib)

