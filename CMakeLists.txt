
cmake_minimum_required(VERSION 3.10.2)   # 设置CMake的最低版本要求
project(test_monitor LANGUAGES CXX)       # 设置项目名称以及语言为C++
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")  # 设置CMake模块的路径
set(CMAKE_CXX_STANDARD 17)               # 设置C++标准为C++17

add_subdirectory(rpc_manager)            # 添加rpc_manager子目录
add_subdirectory(test_monitor)           # 添加test_monitor子目录
add_subdirectory(proto)                  # 添加proto子目录
add_subdirectory(display_monitor)        # 添加display_monitor子目录