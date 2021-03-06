project(xbmc-addons-depends-windows)

cmake_minimum_required(VERSION 2.8)

list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR})

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

include(ExternalProject)

if(NOT CMAKE_INSTALL_PREFIX)
  message(FATAL_ERROR "CMAKE_INSTALL_PREFIX (${CMAKE_INSTALL_PREFIX}) is not a valid target directory.")
else()
  file(TO_CMAKE_PATH "${CMAKE_INSTALL_PREFIX}" CMAKE_INSTALL_PREFIX)
endif()
get_filename_component(CMAKE_INSTALL_PREFIX "${CMAKE_INSTALL_PREFIX}" ABSOLUTE)
list(APPEND CMAKE_PREFIX_PATH ${CMAKE_INSTALL_PREFIX})

function(add_internal id url inputfile)
  externalproject_add(${id}
                      URL ${url}
                      PREFIX build/${id}
                      CONFIGURE_COMMAND ""
                      BUILD_COMMAND ""
                      INSTALL_COMMAND ${CMAKE_COMMAND}
                                      -DINPUTDIR=${PROJECT_BINARY_DIR}/build/${id}/src/${id}
                                      -DINPUTFILE=${inputfile}
                                      -DDESTDIR=${CMAKE_INSTALL_PREFIX}
                                      -P ${PROJECT_SOURCE_DIR}/install.cmake
                     )
endfunction()

#find_package(7Zip REQUIRED)

file(GLOB_RECURSE download_input_files prebuilt/*.txt)
foreach(file ${download_input_files})
  if(NOT file MATCHES install.txt)
    file(STRINGS ${file} def)
    get_filename_component(dir ${file} PATH)
    string(REPLACE " " ";" def ${def})
    list(GET def 0 id)
    list(GET def 1 url)
    add_internal(${id} ${url} ${dir}/install.txt)
  endif()
endforeach()
