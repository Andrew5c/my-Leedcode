cmake_minimum_required (VERSION 3.7)
set(CMAKE_CXX_STANDARD 14)

project (fun_2048)
set (SRCS ./src/main_2048.cpp ./src/menu.cpp ./src/global.cpp ./src/menu-graphics.cpp ./src/game-graphics.cpp)

include_directories (./src/headers/)
add_executable (2048 ${SRCS})
