cmake_minimum_required(VERSION 3.0)

#Avoid cmake from test compiling to host
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)


#service
project (service)

#check if conan is already run outside of project
if (NOT DEFINED CONAN_DEPENDENCIES)
  include(${CMAKE_CURRENT_BINARY_DIR}/conanbuildinfo.cmake OPTIONAL RESULT_VARIABLE HAS_CONAN)
  if (NOT HAS_CONAN)
    message(FATAL_ERROR "missing conanbuildinfo.cmake did you forget to run conan install ?")
  endif()
endif()
conan_basic_setup()


include(os)

set(SOURCES
    service.cpp stream.cpp
)

os_add_executable(stream "STREAM benchmark" ${SOURCES})
os_add_stdout(stream default_stdout)
