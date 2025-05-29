cmake_minimum_required(VERSION 3.15)
project(PathTRacingInAWeekend LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)


# ==== Setup spdlog ====
include(FetchContent)
# Clone + build spdlog into thirdparty/spdlog
FetchContent_Declare(
    spdlog
    GIT_REPOSITORY https://github.com/gabime/spdlog.git
    GIT_TAG v1.13.0  # Change to the version you want
    SOURCE_DIR ${CMAKE_SOURCE_DIR}/thirdparty/spdlog
)

FetchContent_MakeAvailable(spdlog)

# ==== Add your project ====
add_executable(my_project src/main.cpp)

# Link spdlog
target_link_libraries(my_project PRIVATE spdlog::spdlog)

# Optional: if using warnings
target_compile_features(my_project PRIVATE cxx_std_23)