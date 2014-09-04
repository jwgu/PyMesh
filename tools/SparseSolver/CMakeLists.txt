# Include this directory
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

# Numerics package.
FIND_PACKAGE(NumericLibraries)
INCLUDE_DIRECTORIES(${NUMERIC_INCLUDE_DIRS})
LINK_DIRECTORIES(${NUMERIC_LIBRARY_DIRS})

# Enumerate source files
FILE(GLOB_RECURSE SRC_FILES *.cpp)
FILE(GLOB_RECURSE INC_FILES *.h *.hh)

ADD_LIBRARY(lib_SparseSolver SHARED ${SRC_FILES} ${INC_FILES})
SET_TARGET_PROPERTIES(lib_SparseSolver PROPERTIES OUTPUT_NAME "SparseSolver")
TARGET_LINK_LIBRARIES(lib_SparseSolver Mesh ${NUMERIC_LIBRARIES})

set(SWG_FILES PySparseSolver.i)

# Generate wrapper for python
SET_SOURCE_FILES_PROPERTIES(${SWG_FILES} PROPERTIES CPLUSPLUS ON)
SET_SOURCE_FILES_PROPERTIES(${SWG_FILES} PROPERTIES CMAKE_SWIG_FLAGS "-includeall")
SWIG_ADD_MODULE(PySparseSolver python ${SWG_FILES} ${INC_FILES})
SWIG_LINK_LIBRARIES(PySparseSolver ${PYTHON_LIBRARIES} lib_SparseSolver Mesh)

# Boudle lib, swig wrapper into a single target.
# Usage: make SparseSolver
ADD_CUSTOM_TARGET(SparseSolver DEPENDS lib_SparseSolver _PySparseSolver)