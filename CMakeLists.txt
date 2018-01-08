cmake_minimum_required(VERSION 2.8)
project (nuQoin)
# detect operating system
message(STATUS "We are on a ${CMAKE_SYSTEM_NAME} system")

if(${CMAKE_SYSTEM_NAME} STREQUAL "Darwin")
    # openssl
    SET (MAC_OPENSSL_ROOT_DIR "/usr/local/opt/openssl" )

    # level db
    message(STATUS "leveldb depedency check")
    find_path(LEVELDB_INCLUDE
        NAMES leveldb/db.h
        PATHS $ENV{LEVELDB_ROOT}/include /opt/local/include /usr/local/include /usr/include
        DOC "leveldb include path" )
    find_library(LEVELDB_LIBRARY
        NAMES libleveldb.a libleveldb.dylib
        PATHS /usr/lib /usr/local/lib $ENV{LEVELDB_ROOT}/lib
        DOC "leveldb library path" )
    if(LEVELDB_INCLUDE AND LEVELDB_LIBRARY)
        message(STATUS "leveldb detected -I${LEVELDB_INCLUDE} -L${LEVELDB_LIBRARY})")
    else()
        message(WARNING "leveldb not detected")
    endif()

    # jsoncpp
    message(STATUS "jsoncpp depedency check")
    find_path(JSONCPP_INCLUDE
        NAMES json/json.h
        PATHS $ENV{JSONCPP_ROOT}/include /opt/local/include /usr/local/include /usr/include
        DOC "jsoncpp include path" )
    find_library(JSONCPP_LIBRARY
        NAMES libjsoncpp.a libjsoncpp.dylib
        PATHS /usr/lib /usr/local/lib $ENV{JSONCPP_ROOT}/lib
        DOC "jsoncpp library path" )
    if(JSONCPP_INCLUDE AND JSONCPP_LIBRARY)
        message(STATUS "jsoncpp detected -I${JSONCPP_INCLUDE} -L${JSONCPP_LIBRARY})")
    else()
        message(WARNING "jsoncpp not detected")
    endif()

    SET (MAC_CXX_FLAGS "-I${MAC_OPENSSL_ROOT_DIR}/include -I${LEVELDB_INCLUDE} -I${JSONCPP_INCLUDE}" )
    SET (MAC_LINKER_FLAGS "-L${MAC_OPENSSL_ROOT_DIR}/lib -L${LEVELDB_LIBRARY} -L${JSONCPP_LIBRARY}" )
endif()

if(IS_LOOKUP_NODE)
    if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
        SET( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GCC_COVERAGE_COMPILE_FLAGS} -pthread -std=c++14 -Wall -DSTAT_TEST -ggdb -DIS_LOOKUP_NODE" )
        SET( CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${GCC_COVERAGE_LINK_FLAGS} -pthread -ljsoncpp -lboost_system -lboost_filesystem -std=c++14 -Wall -DSTAT_TEST -DIS_LOOKUP_NODE" )
    endif()
    if(${CMAKE_SYSTEM_NAME} STREQUAL "Darwin")
        SET( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GCC_COVERAGE_COMPILE_FLAGS} -pthread -stdlib=libc++ -std=c++14 -Wall -DSTAT_TEST -ggdb -DIS_LOOKUP_NODE ${MAC_CXX_FLAGS}" )
        SET( CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${GCC_COVERAGE_LINK_FLAGS} -pthread -ljsoncpp -lboost_system -lboost_filesystem -std=c++14 -Wall -DSTAT_TEST -DIS_LOOKUP_NODE ${MAC_LINKER_FLAGS}" )
    endif()
    if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
        # To-do
    endif()
endif()

if(NOT IS_LOOKUP_NODE)
    if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
        SET( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GCC_COVERAGE_COMPILE_FLAGS} -pthread -std=c++14 -Wall -DSTAT_TEST -ggdb" )
        SET( CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${GCC_COVERAGE_LINK_FLAGS} -pthread -ljsoncpp -lboost_system -lboost_filesystem -std=c++14 -Wall -DSTAT_TEST" )
    endif()
    if(${CMAKE_SYSTEM_NAME} STREQUAL "Darwin")
        SET( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GCC_COVERAGE_COMPILE_FLAGS} -pthread -stdlib=libc++ -std=c++14 -Wall -DSTAT_TEST -ggdb ${MAC_CXX_FLAGS}" )
        SET( CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${GCC_COVERAGE_LINK_FLAGS} -pthread -ljsoncpp -lboost_system -lboost_filesystem -std=c++14 -Wall -DSTAT_TEST ${MAC_LINKER_FLAGS}" )
    endif()
    if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
        # To-do
    endif()
endif()

add_subdirectory (src)
add_subdirectory (tests)
