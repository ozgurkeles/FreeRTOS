cmake_minimum_required(VERSION 3.5)

project(Wasm_Emscripten LANGUAGES C)

add_definitions("
    -DprojCOVERAGE_TEST=1
    ")

set(DEMO_COMMON_SOURCE
    Demo/Common/Minimal/AbortDelay.c
    Demo/Common/Minimal/BlockQ.c
    Demo/Common/Minimal/blocktim.c
    Demo/Common/Minimal/countsem.c
    Demo/Common/Minimal/death.c
    Demo/Common/Minimal/dynamic.c
    Demo/Common/Minimal/EventGroupsDemo.c
    Demo/Common/Minimal/flop.c
    Demo/Common/Minimal/GenQTest.c
    Demo/Common/Minimal/integer.c
    Demo/Common/Minimal/IntSemTest.c
    Demo/Common/Minimal/MessageBufferAMP.c
    Demo/Common/Minimal/MessageBufferDemo.c
    Demo/Common/Minimal/PollQ.c
    Demo/Common/Minimal/QPeek.c
    Demo/Common/Minimal/QueueOverwrite.c
    Demo/Common/Minimal/QueueSet.c
    Demo/Common/Minimal/QueueSetPolling.c
    Demo/Common/Minimal/recmutex.c
    Demo/Common/Minimal/semtest.c
    Demo/Common/Minimal/StaticAllocation.c
    Demo/Common/Minimal/StreamBufferDemo.c
    Demo/Common/Minimal/StreamBufferInterrupt.c
    Demo/Common/Minimal/TaskNotify.c
    Demo/Common/Minimal/TimerDemo.c
)

add_executable(Wasm_Emscripten code_coverage_additions.c console.c main_blinky.c main.c main_full.c run-time-stats-utils.c ${DEMO_COMMON_SOURCE})

target_include_directories(Wasm_Emscripten
    PRIVATE
    .
    Demo/Common/include
    FreeRTOS-Plus/Source/FreeRTOS-Plus-Trace/Include
)

if( ${CMAKE_SYSTEM_NAME} MATCHES "Emscripten")
    add_definitions("-DEMSCRIPTEN")
    set(CMAKE_EXECUTABLE_SUFFIX ".html")
    set(EMCC_FLAGS "-sWASM_WORKERS")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${EMCC_FLAGS}")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${EMCC_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -s EXPORTED_FUNCTIONS=[_main,_malloc,_free] -s EXPORTED_RUNTIME_METHODS=[ccall,cwrap] ${EMCC_FLAGS}")
endif()

set(FREERTOS_CONFIG_FILE_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}" CACHE STRING "Absolute path to the directory with FreeRTOSConfig.h")
set(FREERTOS_PORT "Emscripten_Wasm" CACHE STRING "FreeRTOS port name")


add_subdirectory(freertos_kernel)

target_link_libraries(Wasm_Emscripten freertos_kernel)
