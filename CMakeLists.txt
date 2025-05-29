cmake_minimum_required(VERSION 3.15)

set(PROJECT_NAME PathTracingInAWeekend)

project(${PROJECT_NAME} LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

include(ExternalProject)

# Install spdlog to thirdparty/spdlog
set(SPDLOG_INSTALL_DIR ${CMAKE_SOURCE_DIR}/thirdparty/spdlog)
file(MAKE_DIRECTORY ${SPDLOG_INSTALL_DIR})

set(SPDLOG_SOURCE_DIR ${CMAKE_BINARY_DIR}/spdlog_ep/src/spdlog_ep)

# Define absolute path for Git safe.directory
set(SPDLOG_SAFE_DIR "${SPDLOG_SOURCE_DIR}")

ExternalProject_Add(spdlog_ep
    PREFIX ${CMAKE_BINARY_DIR}/spdlog_ep
    INSTALL_DIR ${SPDLOG_INSTALL_DIR}
    SOURCE_DIR ${SPDLOG_SOURCE_DIR}

    DOWNLOAD_COMMAND
        ${CMAKE_COMMAND} -E echo "Marking ${SPDLOG_SAFE_DIR} as safe for Git" &&
        git config --global --add safe.directory ${SPDLOG_SAFE_DIR} &&
        git clone --branch v1.15.3 https://github.com/gabime/spdlog.git ${SPDLOG_SOURCE_DIR}

    CMAKE_ARGS
        -DCMAKE_INSTALL_PREFIX=${SPDLOG_INSTALL_DIR}
        -DCMAKE_POSITION_INDEPENDENT_CODE=ON
        -DSPDLOG_BUILD_SHARED=OFF
        -DSPDLOG_BUILD_EXAMPLES=OFF
        -DSPDLOG_BUILD_TESTS=OFF
        -DSPDLOG_BUILD_BENCH=OFF
        -DSPDLOG_INSTALL=ON
        -DSPDLOG_FMT_EXTERNAL=OFF

    BUILD_BYPRODUCTS ${SPDLOG_INSTALL_DIR}/lib/libspdlog.a
)

# Your app
add_executable(${PROJECT_NAME} src/main.cpp)

# Link to spdlog static lib
add_dependencies(${PROJECT_NAME} spdlog_ep)
target_include_directories(${PROJECT_NAME} PRIVATE ${SPDLOG_INSTALL_DIR}/include)
target_link_directories(${PROJECT_NAME} PRIVATE ${SPDLOG_INSTALL_DIR}/lib)
target_link_libraries(${PROJECT_NAME} PRIVATE spdlog)
