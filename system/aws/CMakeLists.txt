if(USE_AWS)
    # minimal CMakeLists.txt for the AWS SDK for C++
    cmake_minimum_required(VERSION 2.8)

    project(aws)
    set(AWS_SDK_CPP_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/aws-sdk-cpp)
    if (CMAKE_COMPILER_IS_GNUCXX AND NOT CMAKE_CXX_COMPILER_VERSION VERSION_LESS 7.0.0)
      set(EXTERNAL_SQS_CXX_FLAGS "-Wno-error=implicit-fallthrough -Wno-error=int-in-bool-context -Wno-error=format-nonliteral" )
    endif()

    include(ExternalProject)
    ExternalProject_Add(
        aws-sdk-cpp
        SOURCE_DIR ${AWS_SDK_CPP_SOURCE_DIR}
        BINARY_DIR ${CMAKE_CURRENT_BINARY_DIR}/aws-sdk-cpp
        CMAKE_ARGS -DCUSTOM_MEMORY_MANAGEMENT:string=0 -DCMAKE_CXX_FLAGS=${EXTERNAL_SQS_CXX_FLAGS}
        BUILD_COMMAND ${CMAKE_MAKE_PROGRAM} LDFLAGS=-Wl,-rpath-link,${LIB_PATH} aws-cpp-sdk-sqs
        INSTALL_COMMAND "")

    add_library(aws-cpp-sdk-core SHARED IMPORTED GLOBAL)
    add_library(aws-cpp-sdk-sqs SHARED IMPORTED GLOBAL)
    set_property(TARGET aws-cpp-sdk-core
        PROPERTY IMPORTED_LOCATION ${CMAKE_CURRENT_BINARY_DIR}/aws-sdk-cpp/aws-cpp-sdk-core/libaws-cpp-sdk-core.so)
    set_property(TARGET aws-cpp-sdk-sqs
        PROPERTY IMPORTED_LOCATION ${CMAKE_CURRENT_BINARY_DIR}/aws-sdk-cpp/aws-cpp-sdk-sqs/libaws-cpp-sdk-sqs.so)
    add_dependencies(aws-cpp-sdk-core aws-sdk-cpp)
    add_dependencies(aws-cpp-sdk-sqs aws-sdk-cpp)

    install(CODE "set(ENV{LD_LIBRARY_PATH} \"\$ENV{LD_LIBRARY_PATH}:${PROJECT_BINARY_DIR}:${PROJECT_BINARY_DIR}/aws-sdk-cpp/aws-cpp-sdk-core:${PROJECT_BINARY_DIR}/aws-sdk-cpp/aws-cpp-sdk-sqs\")")
    install(FILES
        ${CMAKE_CURRENT_SOURCE_DIR}/aws-sdk-cpp/LICENSE.txt
        DESTINATION "."
        RENAME aws-sdk-cpp-LICENSE.txt)
    install(PROGRAMS
        ${CMAKE_CURRENT_BINARY_DIR}/aws-sdk-cpp/aws-cpp-sdk-core/libaws-cpp-sdk-core.so
        ${CMAKE_CURRENT_BINARY_DIR}/aws-sdk-cpp/aws-cpp-sdk-sqs/libaws-cpp-sdk-sqs.so
        DESTINATION lib)

endif()
