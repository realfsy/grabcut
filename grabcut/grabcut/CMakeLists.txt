# compile mex
set(CPP_FILE mexAdd)

# set up matlab libraries
set(CPP_FILE_NAME ${CPP_FILE}.cpp)
find_package( OpenCV REQUIRED )
set(3rdparty "3rdparty")
aux_source_directory(. srcLists)
message(STATUS ${srcLists})
INCLUDE_DIRECTORIES(${MATLAB_INCLUDE_DIR}
					"include"
					"${3rdparty}"
					"${3rdparty}/MeshLib/core"
					"${OpenCV_INCLUDE_DIRS}"
					)
add_library(${CPP_FILE} SHARED ${CPP_FILE_NAME} ${srcLists} ${CMAKE_SOURCE_DIR}/Matlabdef.def)
target_link_libraries(${CPP_FILE} ${MATLAB_LIBRARIES} ${OpenCV_LIBS})

# 32-bit or 64-bit mex
if(WIN32)
  if (CMAKE_CL_64)
      SET_TARGET_PROPERTIES(${CPP_FILE} PROPERTIES SUFFIX .mexw64)
  else(CMAKE_CL_64)
      SET_TARGET_PROPERTIES(${CPP_FILE} PROPERTIES SUFFIX .mexw32)
  endif(CMAKE_CL_64)
else(WIN32)
  if (CMAKE_SIZEOF_VOID_P MATCHES "8")
      SET_TARGET_PROPERTIES(${CPP_FILE} PROPERTIES SUFFIX .mexa64 PREFIX "")
  else(CMAKE_SIZEOF_VOID_P MATCHES "8")
      SET_TARGET_PROPERTIES(${CPP_FILE} PROPERTIES SUFFIX .mexglx PREFIX "")
  endif (CMAKE_SIZEOF_VOID_P MATCHES "8")
endif(WIN32)

# install to /bin by default
install(TARGETS ${CPP_FILE} DESTINATION ../bin)