# ##############################################################################
# SWIG (WIN32)
# ##############################################################################
if(WIN32
   AND (BUILD_PYTHON
        OR BUILD_JAVA
        OR BUILD_DOTNET))
  message(CHECK_START "Fetching SWIG")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  configure_file(${CMAKE_CURRENT_SOURCE_DIR}/SWIG.CMakeLists.txt.in
                 ${CMAKE_CURRENT_BINARY_DIR}/SWIG/CMakeLists.txt @ONLY)

  execute_process(
    COMMAND ${CMAKE_COMMAND} -H. -Bproject_build -G "${CMAKE_GENERATOR}"
    RESULT_VARIABLE result
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/SWIG)
  if(result)
    message(FATAL_ERROR "CMake step for SWIG failed: ${result}")
  endif()

  execute_process(
    COMMAND ${CMAKE_COMMAND} --build project_build --config ${CMAKE_BUILD_TYPE}
    RESULT_VARIABLE result
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/SWIG)
  if(result)
    message(FATAL_ERROR "Build step for SWIG failed: ${result}")
  endif()

  set(SWIG_EXECUTABLE
      ${CMAKE_CURRENT_BINARY_DIR}/SWIG/source/swig.exe
      CACHE INTERNAL "swig.exe location" FORCE)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

include(FetchContent)
set(BUILD_SHARED_LIBS OFF)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(BUILD_TESTING OFF)

# ##############################################################################
# ZLIB
# ##############################################################################
if(BUILD_ZLIB)
  message(CHECK_START "Fetching ZLIB")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  FetchContent_Declare(
    zlib
    GIT_REPOSITORY "https://github.com/madler/ZLIB.git"
    GIT_TAG "v1.2.11"
    PATCH_COMMAND git apply "${CMAKE_CURRENT_LIST_DIR}/../../patches/ZLIB.patch")
  FetchContent_MakeAvailable(zlib)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

# ##############################################################################
# ABSEIL-CPP
# ##############################################################################
if(BUILD_absl)
  message(CHECK_START "Fetching Abseil-cpp")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  set(ABSL_ENABLE_INSTALL ON)
  FetchContent_Declare(
    absl
    GIT_REPOSITORY "https://github.com/abseil/abseil-cpp.git"
    GIT_TAG "20200225.2"
    PATCH_COMMAND git apply "${CMAKE_CURRENT_LIST_DIR}/../../patches/abseil-cpp-20200225.2.patch")
  FetchContent_MakeAvailable(absl)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

# ##############################################################################
# GFLAGS
# ##############################################################################
if(BUILD_gflags)
  message(CHECK_START "Fetching gflags")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  set(INSTALL_HEADERS ON)
  set(INSTALL_SHARED_LIBS OFF)
  set(INSTALL_SHARED_LIBS OFF)
  set(BUILD_STATIC_LIBS ON)
  set(INSTALL_STATIC_LIBS ON)
  FetchContent_Declare(
    gflags
    GIT_REPOSITORY "https://github.com/gflags/gflags.git"
    GIT_TAG "v2.2.2"
    PATCH_COMMAND git apply "${CMAKE_CURRENT_LIST_DIR}/../../patches/gflags-v2.2.2.patch")
  FetchContent_MakeAvailable(gflags)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

# ##############################################################################
# GLOG
# ##############################################################################
if(BUILD_glog)
  message(CHECK_START "Fetching glog")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  FetchContent_Declare(
    glog
    GIT_REPOSITORY "https://github.com/google/glog.git"
    GIT_TAG "v0.4.0"
    PATCH_COMMAND git apply "${CMAKE_CURRENT_LIST_DIR}/../../patches/glog-v0.4.0.patch")
  set(WITH_GFLAGS OFF)
  FetchContent_MakeAvailable(glog)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

# ##############################################################################
# SCIP
# ##############################################################################
if(BUILD_SCIP)
  message(CHECK_START "Fetching SCIP")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  set(SHARED OFF)
  set(READLINE OFF)
  set(GMP OFF)
  set(PAPILO OFF)
  set(ZIMPL OFF)
  set(IPOPT OFF)

  set(TPI "none" CACHE STRING "Scip param")
  set(EXPRINT "none" CACHE STRING "Scip param")
  set(LPS "none" CACHE STRING "Scip param")
  set(SYM "none" CACHE STRING "Scip param")
  FetchContent_Declare(
    scip
    URL "${CMAKE_CURRENT_LIST_DIR}/../../dependencies/archives/scip-7.0.1.tgz"
    PATCH_COMMAND git apply --ignore-whitespace "${CMAKE_CURRENT_LIST_DIR}/../../patches/scip-7.0.1.patch")
  FetchContent_MakeAvailable(scip)
  set(LPI_GLOP_SRC ${scip_SOURCE_DIR}/src/lpi/lpi_glop.cpp PARENT_SCOPE)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

# ##############################################################################
# Protobuf
# ##############################################################################
if(BUILD_Protobuf)
  message(CHECK_START "Fetching Protobuf")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  set(protobuf_BUILD_TESTS OFF)
  set(protobuf_BUILD_EXPORT OFF)
  set(protobuf_MSVC_STATIC_RUNTIME OFF)
  # FetchContent_Declare(SOURCE_SUBDIR) was introduced in 3.18
  if(${CMAKE_VERSION} VERSION_GREATER_EQUAL "3.18")
    FetchContent_Declare(
      protobuf
      GIT_REPOSITORY "https://github.com/protocolbuffers/protobuf.git"
      GIT_TAG "v3.12.2"
      PATCH_COMMAND git apply "${CMAKE_CURRENT_LIST_DIR}/../../patches/protobuf-v3.12.2.patch"
      SOURCE_SUBDIR cmake)
    FetchContent_MakeAvailable(protobuf)
  else()
    fetch_git_dependency(
      NAME Protobuf
      REPOSITORY "https://github.com/protocolbuffers/protobuf.git"
      TAG "v3.12.2"
      PATCH_COMMAND "git apply \"${CMAKE_CURRENT_LIST_DIR}/../../patches/protobuf-v3.12.2.patch\""
      SOURCE_SUBDIR cmake
      )
  endif()
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

# ##############################################################################
# Coinutils
# ##############################################################################
if(BUILD_CoinUtils)
  message(CHECK_START "Fetching CoinUtils")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  FetchContent_Declare(
    CoinUtils
    GIT_REPOSITORY "https://github.com/Mizux/CoinUtils.git"
    GIT_TAG "stable/2.11")
  FetchContent_MakeAvailable(CoinUtils)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

# ##############################################################################
# Osi
# ##############################################################################
if(BUILD_Osi)
  message(CHECK_START "Fetching Osi")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  FetchContent_Declare(
    Osi
    GIT_REPOSITORY "https://github.com/Mizux/Osi.git"
    GIT_TAG "stable/0.108")
  FetchContent_MakeAvailable(Osi)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

# ##############################################################################
# Clp
# ##############################################################################
if(BUILD_Clp)
  message(CHECK_START "Fetching Clp")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  FetchContent_Declare(
    Clp
    GIT_REPOSITORY "https://github.com/Mizux/Clp.git"
    GIT_TAG "stable/1.17.4")
  FetchContent_MakeAvailable(Clp)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

# ##############################################################################
# Cgl
# ##############################################################################
if(BUILD_Cgl)
  message(CHECK_START "Fetching Cgl")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  FetchContent_Declare(
    Cgl
    GIT_REPOSITORY "https://github.com/Mizux/Cgl.git"
    GIT_TAG "stable/0.60")
  FetchContent_MakeAvailable(Cgl)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()

# ##############################################################################
# Cbc
# ##############################################################################
if(BUILD_Cbc)
  message(CHECK_START "Fetching Cbc")
  list(APPEND CMAKE_MESSAGE_INDENT "  ")
  FetchContent_Declare(
    Cbc
    GIT_REPOSITORY "https://github.com/Mizux/Cbc.git"
    GIT_TAG "stable/2.10")
  FetchContent_MakeAvailable(Cbc)
  list(POP_BACK CMAKE_MESSAGE_INDENT)
  message(CHECK_PASS "fetched")
endif()
