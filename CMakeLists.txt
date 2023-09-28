cmake_minimum_required(VERSION 3.0.0)

# Project version
file(STRINGS ${CMAKE_CURRENT_SOURCE_DIR}/VERSION PROJECT_VER LIMIT_COUNT 1)
string(REPLACE "." ";" PROJECT_VER_LIST ${PROJECT_VER})
list(GET PROJECT_VER_LIST 0 PROJECT_VER_MAJOR)
list(GET PROJECT_VER_LIST 1 PROJECT_VER_MINOR)
list(GET PROJECT_VER_LIST 2 PROJECT_VER_PATCH)

project(xlib VERSION ${PROJECT_VER} LANGUAGES C)

option(CROSS_COMPILE_ARM64 "Cross compile for ARM64" OFF)
option(KAFKA "Kafka sink support" ON)
option(REDIS "Redis sink support" ON)
option(NATS "NATS sink support" ON)
option(MOSQUITTO "mosquitto src-sink support" ON)
option(PAHO "paho.mqtt.c src-sink support" ON)
option(INFLUXDB "influxdb sink support" ON)

include(CTest)
enable_testing()

# List of header files
set(HEADERS
    include/datalog.h
    include/logger.h
    include/squeue.h
)

# List of source files
set(SOURCES 
    src/datalog.c
    src/logger.c
    src/squeue.c    
    src/configuration.c
    src/xlib.c
)

include_directories(include)

if(KAFKA)
    message("KAFKA flag is defined.")
    list(APPEND SOURCES src/kafka_sink.c)
else()
    message("KAFKA flag is not defined!")
endif()

if(REDIS)
    message("REDIS flag is defined.")
    list(APPEND SOURCES src/redis_sink.c)
else()
    message("REDIS flag is not defined!")
endif()

if(NATS)
    message("NATS flag is defined.")
    list(APPEND SOURCES src/nats_sink.c)
else()
    message("NATS flag is not defined!")
endif()

if(INFLUXDB)
    message("INFLUXDB flag is defined.")
    list(APPEND SOURCES src/influxdb_sink.c)
else()
    message("INFLUXDB flag is not defined!")
endif()

if(MOSQUITTO)
    message("MOSQUITTO flag is defined.")
    list(APPEND SOURCES src/mosq_sink.c)
    list(APPEND SOURCES src/mosq_src.c)
else()
    message("MOSQUITTO flag is not defined!")
endif()

if(PAHO)
    message("PAHO-mqtt flag is defined.")
    list(APPEND SOURCES src/mqtt_sink.c)
    list(APPEND SOURCES src/mqtt_src.c)
else()
    message("PAHO-mqtt flag is not defined!")
endif()

add_library(xlib ${SOURCES})

set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)
