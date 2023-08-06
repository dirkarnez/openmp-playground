cmake_minimum_required(VERSION 3.18)

project("openmp-playground")

SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static")

find_package(OpenMP)

if(OpenMP_FOUND)
    message(STATUS "YES")
else()
    message(STATUS "NO")
endif()


if (OpenMP_FOUND AND OpenMP_CXX_VERSION VERSION_GREATER_EQUAL 3.0.0)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
  set(MLPACK_LIBRARIES ${MLPACK_LIBRARIES} ${OpenMP_CXX_LIBRARIES})
else ()
  # Disable warnings for all the unknown OpenMP pragmas.
  if (NOT MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-unknown-pragmas")
  else ()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4068")
  endif ()
  set(OpenMP_CXX_FLAGS "")
endif ()

add_executable(${PROJECT_NAME}
    "src/main.cpp"
)

# if(NOT TARGET OpenMP::OpenMP_CXX)
#   find_package(Threads REQUIRED)
#   add_library(OpenMP::OpenMP_CXX IMPORTED INTERFACE)
#   set_property(TARGET OpenMP::OpenMP_CXX
#       PROPERTY INTERFACE_COMPILE_OPTIONS ${OpenMP_CXX_FLAGS})
#   # Only works if the same flag is passed to the linker; use CMake 3.9+ otherwise (Intel, AppleClang).
#   set_property(TARGET OpenMP::OpenMP_CXX
#       PROPERTY INTERFACE_LINK_LIBRARIES ${OpenMP_CXX_FLAGS} Threads::Threads)
# endif()
  
target_link_libraries(${PROJECT_NAME} INTERFACE OpenMP::OpenMP_CXX)
