cmake_minimum_required(VERSION 3.10)
project(neural-net)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_COMPILER g++)

# Find Eigen
find_package(Eigen3 3.3 REQUIRED NO_MODULE)

# Source files for the core library
set(SOURCES
    src/tensor.cpp
    src/layer.cpp
    src/propagation.cpp
    src/network.cpp
    src/optimizer.cpp
    src/trainer.cpp
    src/layers/dense.cpp
    src/layers/conv.cpp
    src/layers/activation.cpp
    src/utils/activation_functions.cpp
    src/utils/loss_functions.cpp
    src/utils/math_utils.cpp
)

# Create a library from core sources
add_library(neural_net_lib ${SOURCES})
target_include_directories(neural_net_lib PUBLIC 
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_SOURCE_DIR}/src
)
target_link_libraries(neural_net_lib PUBLIC Eigen3::Eigen)

# Main executable
add_executable(myapp examples/simple_net.cpp)
target_link_libraries(myapp neural_net_lib)

# Optional: Enable testing
enable_testing()

# Add test executable
set(TEST_SOURCES
    tests/test_tensor.cpp
    tests/test_layers.cpp
    tests/test_network.cpp
    tests/test_breaking_weight.cpp
)

add_executable(run_tests ${TEST_SOURCES})
target_link_libraries(run_tests neural_net_lib)
add_test(NAME CoreTests COMMAND run_tests)

# Compiler flags for better error messages
if(MSVC)
    target_compile_options(neural_net_lib PRIVATE /W4)
    target_compile_options(myapp PRIVATE /W4)
else()
    target_compile_options(neural_net_lib PRIVATE -Wall -Wextra -Wpedantic)
    target_compile_options(myapp PRIVATE -Wall -Wextra -Wpedantic)
endif()