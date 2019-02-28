#CMakelist driver
if (${HAS_PREFIX})
    set(DRIVER_COMPILE_FLAGS "-O2 -c -D__STDC_FORMAT_MACROS -DQCONF_INTERNAL -fPIC -g -pthread -Wall")
else()
    set(DRIVER_COMPILE_FLAGS "-O2 -c -D__STDC_FORMAT_MACROS -fPIC -g -pthread -Wall")
endif()
#set(LINK_FLAGS "-lpthread")

project(qconf_driver)
cmake_minimum_required(VERSION 2.6)
include_directories(
    ${PROJECT_SOURCE_DIR}/include
    ${PROJECT_SOURCE_DIR}/../../base
    ${PROJECT_SOURCE_DIR}/../../base/qlibc
    ${PROJECT_SOURCE_DIR}/../../deps/zookeeper/_install/include/zookeeper
    )

aux_source_directory(src DIR_SRCS)
aux_source_directory(../../base DIR_SRCS)
aux_source_directory(../../base/qlibc DIR_SRCS)

set_source_files_properties(../../base/qlibc/md5.c PROPERTIES LANGUAGE CXX )
set_source_files_properties(../../base/qlibc/qhash.c PROPERTIES LANGUAGE CXX )
set_source_files_properties(../../base/qlibc/qhasharr.c PROPERTIES LANGUAGE CXX )

add_definitions(${DRIVER_COMPILE_FLAGS})

add_library(qconf STATIC ${DIR_SRCS})

# link libpthread
find_package (Threads)
target_link_libraries (qconf ${CMAKE_THREAD_LIBS_INIT})

# link static libzookeeper_mt
target_link_libraries(qconf zookeeper_mt)
install(TARGETS qconf 
        ARCHIVE DESTINATION 
        ${CMAKE_INSTALL_PREFIX}/lib)

file(GLOB QCONF_HEADERS "include/*.h")
install(FILES ${QCONF_HEADERS} DESTINATION ${CMAKE_INSTALL_PREFIX}/include/qconf/)
