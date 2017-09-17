cmake_minimum_required(VERSION 2.6)
project (python-moos)
find_package(MOOS 10 REQUIRED)

set(Python_ADDITIONAL_VERSIONS 3.*)
find_package(PythonLibs)
if(PYTHONLIBS_FOUND)
  include_directories("${PYTHON_INCLUDE_DIRS}")
else()
  message(FATAL_ERROR "Unable to find PythonLibs.")
endif()

string(REGEX MATCH "^([0-9]+)\\.([0-9]+)*" pyver ${PYTHONLIBS_VERSION_STRING} )
string(REPLACE "." "" pyver ${pyver})
string(CONCAT pyver "py" ${pyver})



find_package(Boost REQUIRED python-${pyver})
set(Boost_USE_STATIC_LIBS     ON)
set(Boost_USE_MULTITHREADED    ON)
set(Boost_USE_STATIC_RUNTIME     ON)

if(NOT DEFINED CMAKE_RUNTIME_OUTPUT_DIRECTORY)
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/bin)
endif()

if(NOT DEFINED CMAKE_LIBRARY_OUTPUT_DIRECTORY)
  set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/lib)
endif()
if(NOT DEFINED CMAKE_ARCHIVE_OUTPUT_DIRECTORY)
  set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/lib)
endif()


include_directories(${CMAKE_CURRENT_SOURCE_DIR} ${Boost_INCLUDE_DIRS} ${MOOS_INCLUDE_DIRS})

set(src
pyMOOS.cpp
) 

add_library(pymoos SHARED ${src})

set_target_properties( pymoos
    PROPERTIES
    SUFFIX ".so"
)

set_target_properties(pymoos
  PROPERTIES
  PREFIX "")
  
  


TARGET_LINK_LIBRARIES(pymoos ${Boost_LIBRARIES} ${PYTHON_LIBRARIES} ${MOOS_LIBRARIES})

string(REGEX MATCH "^([0-9]+)\\.([0-9]+)*" python_version ${PYTHONLIBS_VERSION_STRING} )
string(CONCAT python_version "/usr/local/lib/python" ${python_version} "/dist-packages" ) 
message("-- Install path is set to " ${python_version})
install(TARGETS pymoos DESTINATION /usr/local/lib/python3/dist-packages)
