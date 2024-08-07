cmake_minimum_required(VERSION 3.5)

project(hwinfo VERSION 1.0.0 LANGUAGES CXX)

if (WIN32)
    add_definitions(-DWIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
endif()

set (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
set (CMAKE_CXX_STANDARD 11)

include(GNUInstallDirs)

string(COMPARE EQUAL "${CMAKE_SOURCE_DIR}" "${CMAKE_CURRENT_SOURCE_DIR}" PROJECT_IS_TOP_LEVEL)

option(BUILD_EXAMPLES "Build example program" ${PROJECT_IS_TOP_LEVEL})
option(BUILD_TESTING  "Build test program" ${PROJECT_IS_TOP_LEVEL})

# Components
option(HWINFO_OS "Enable OS information" ON)
option(HWINFO_MAINBOARD "Enable mainboard information" ON)
option(HWINFO_CPU "Enable CPU information" ON)
option(HWINFO_DISK "Enable disk information" ON)
option(HWINFO_RAM "Enable RAM information" ON)
option(HWINFO_GPU "Enable GPU information" ON)
option(HWINFO_GPU_OPENCL "Enable OpenCL for more GPU information" OFF)
option(HWINFO_BATTERY "Enable battery information" ON)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

set(HWINFO_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include")
add_subdirectory(src)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/hwinfoConfigVersion.cmake"
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY AnyNewerVersion
)
configure_file(cmake/hwinfoConfig.cmake.in
        "${CMAKE_CURRENT_BINARY_DIR}/hwinfoConfig.cmake"
        @ONLY
)
install(FILES
        "${CMAKE_CURRENT_BINARY_DIR}/hwinfoConfig.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/hwinfoConfigVersion.cmake"
        DESTINATION lib/cmake/hwinfo
)
install(EXPORT hwinfoTargets
        FILE hwinfoTargets.cmake
        NAMESPACE hwinfo::
        DESTINATION lib/cmake/hwinfo
)

if(BUILD_EXAMPLES OR BUILD_TESTING)
    add_subdirectory(examples)
endif()

if(BUILD_TESTING)
    include(CTest)
    add_subdirectory(test)
    add_test(system_info system_info)
    add_test(system_info_stand_alone system_info_stand_alone)
endif()
