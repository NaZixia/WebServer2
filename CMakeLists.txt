project(WebServer LANGUAGES CXX)
cmake_minimum_required(VERSION 3.5.1)

#项目根目录
set(ROOT_DIR ${CMAKE_CURRENT_LIST_DIR})
#头文件路径
set(INC_DIR ${ROOT_DIR}/include)

file(GLOB_RECURSE SOURCE_FILES 
    ${ROOT_DIR}/src/event/*.cpp
    ${ROOT_DIR}/src/http/*.cpp
    ${ROOT_DIR}/src/log/*.cpp
    ${ROOT_DIR}/src/server/*.cpp
    ${ROOT_DIR}/src/thread/*.cpp
    ${ROOT_DIR}/src/timer/*.cpp
    ${ROOT_DIR}/src/utility/*.cpp
    ${ROOT_DIR}/main.cpp)
#message(${LIBEVENT_SOURCES})

#头文件路径添加
include_directories(${INC_DIR})

# 添加可执行文件
add_executable(web_server ${SOURCE_FILES})

# 链接必要的库
target_link_libraries(web_server pthread)
