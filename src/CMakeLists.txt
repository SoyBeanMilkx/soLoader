cmake_minimum_required(VERSION 3.25)
project(ElfLoader)

set(CMAKE_CXX_STANDARD 17)

include_directories(include)

set(SOURCES
        main.cpp
        elf_loader.cpp
        elf_reader.cpp
        memory_manager.cpp
        relocator.cpp
        soinfo_manager.cpp
        utils.cpp
)

find_library(log-lib log)

add_executable(elf_loader ${SOURCES})

target_link_libraries(elf_loader ${log-lib})
