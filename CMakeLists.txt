cmake_minimum_required(VERSION 3.2)

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.23.32.tar.gz"
    SHA1 "720311022e7419d083ad9513ca0e752004f106e2"
)

project(HalideIR)

### Installation (https://github.com/forexample/package-example) {

# Introduce variables:
# * CMAKE_INSTALL_LIBDIR
# * CMAKE_INSTALL_BINDIR
# * CMAKE_INSTALL_INCLUDEDIR
include(GNUInstallDirs)

set(config_install_dir "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")
set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(TARGETS_EXPORT_NAME "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)

# Use:
# * TARGETS_EXPORT_NAME
# * PROJECT_NAME
configure_package_config_file(
    "cmake/template/Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)

install(
    DIRECTORY "src/"
    DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
    FILES_MATCHING
    PATTERN "*.h"
)

install(
    FILES "${project_config}"
    DESTINATION "${config_install_dir}"
)

install(
    EXPORT "${TARGETS_EXPORT_NAME}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)

### }

add_library(
    HalideIR
    src/arithmetic/Deinterleave.cpp
    src/arithmetic/Deinterleave.h
    src/arithmetic/ExprUsesVar.h
    src/arithmetic/Interval.cpp
    src/arithmetic/Interval.h
    src/arithmetic/ModulusRemainder.cpp
    src/arithmetic/ModulusRemainder.h
    src/arithmetic/Scope.h
    src/arithmetic/Simplify.cpp
    src/arithmetic/Simplify.h
    src/arithmetic/Substitute.cpp
    src/arithmetic/Substitute.h
    src/base/Debug.cpp
    src/base/Debug.h
    src/base/Error.cpp
    src/base/Error.h
    src/base/Float16.h
    src/base/Float16Opt.cpp
    src/base/RoundingMode.h
    src/base/Type.cpp
    src/base/Type.h
    src/base/TypeBase.h
    src/base/Util.cpp
    src/base/Util.h
    src/ir/Expr.h
    src/ir/FunctionBase.h
    src/ir/IR.cpp
    src/ir/IR.h
    src/ir/IREquality.cpp
    src/ir/IREquality.h
    src/ir/IRMutator.cpp
    src/ir/IRMutator.h
    src/ir/IROperator.cpp
    src/ir/IROperator.h
    src/ir/IRPrinter.cpp
    src/ir/IRPrinter.h
    src/ir/IRVisitor.cpp
    src/ir/IRVisitor.h
    src/ir/Range.h
    src/tvm/node/container.h
    src/tvm/node/ir_functor.h
    src/tvm/node/memory.h
    src/tvm/node/node.cpp
    src/tvm/node/node.h
)

target_include_directories(
    HalideIR
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/src>
)

target_compile_definitions(
    HalideIR
    PUBLIC
    $<BUILD_INTERFACE:Halide_EXPORTS>
)

hunter_add_package(dmlc-core)
find_package(dmlc CONFIG REQUIRED)
target_link_libraries(HalideIR PUBLIC dmlc::dmlc)

install(
    TARGETS HalideIR
    EXPORT "${TARGETS_EXPORT_NAME}"
    LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
    INCLUDES DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
)
