cmake_minimum_required(VERSION 3.0)

#Avoid cmake from test compiling to host
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

# IncludeOS install location
if (NOT DEFINED INCLUDEOS_PREFIX)
  if (NOT DEFINED ENV{INCLUDEOS_PREFIX})
    set(INCLUDEOS_PREFIX /usr/local/includeos)
  else()
    set(INCLUDEOS_PREFIX $ENV{INCLUDEOS_PREFIX})
  endif()
endif()

if (NOT EXISTS "${INCLUDEOS_PREFIX}/cmake/os.cmake")
  MESSAGE(FATAL_ERROR "IncludeOS does not appear to be installed at ${INCLUDEOS_PREFIX}")
endif()
list(APPEND CMAKE_MODULE_PATH ${INCLUDEOS_PREFIX}/cmake)

#service
project (service)
include(os)

set(SOURCES
  service.cpp
)

os_add_executable(http_demo "IncludeOS Demo Service" ${SOURCES})

# DRIVERS / PLUGINS:

if ("$ENV{PLATFORM}" STREQUAL "x86_solo5")
  os_add_drivers(http_demo solo5net)
else()
  os_add_drivers(http_demo
    virtionet     # Virtio networking
    vmxnet3
    boot_logger   # Display boot information
    # Use "boot --drivers ." to see other drivers
    # virtioblk # Virtio block device
    # ... Others from src/drivers
  )
endif()

# os_add_plugins( ... )

os_install_certificates(http_demo ${CMAKE_CURRENT_BINARY_DIR}/disk/certs)
os_diskbuilder(http_demo ${CMAKE_CURRENT_BINARY_DIR}/disk)
