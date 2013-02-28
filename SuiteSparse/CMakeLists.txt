project(SuiteSparse)

# Set optimized building:
IF(CMAKE_COMPILER_IS_GNUCXX AND NOT CMAKE_BUILD_TYPE MATCHES "Debug")
	SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -mtune=native")
	SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O3 -mtune=native")
ENDIF(CMAKE_COMPILER_IS_GNUCXX AND NOT CMAKE_BUILD_TYPE MATCHES "Debug")

# Global flags:
IF (BUILD_METIS)
	INCLUDE_DIRECTORIES("${METIS_SOURCE_DIR}/include")
ELSE (BUILD_METIS)
	ADD_DEFINITIONS(-DNPARTITION)
ENDIF ( BUILD_METIS)

# Used even for MSVC, so blas function names have the final "_"
if (MSVC)
	ADD_DEFINITIONS(-D__MINGW32__)
endif(MSVC)


include_directories(".")  # Needed for "SourceWrappers/*.c" files

#add_subdirectory(SuiteSparse_config)
#add_subdirectory(AMD)
#add_subdirectory(CAMD)
#add_subdirectory(CCOLAMD)
#add_subdirectory(COLAMD)
#add_subdirectory(CHOLMOD)
#add_subdirectory(UMFPACK)

# SET(SUITESPARSE_INCLUDE_DIRS
	# ${SuiteSparse_SOURCE_DIR}/SuiteSparse_config
	# ${SuiteSparse_SOURCE_DIR}/AMD/Include
	# ${SuiteSparse_SOURCE_DIR}/CAMD/Include
	# ${SuiteSparse_SOURCE_DIR}/COLAMD/Include
	# ${SuiteSparse_SOURCE_DIR}/CCOLAMD/Include
	# ${SuiteSparse_SOURCE_DIR}/CHOLMOD/Include
	# ${SuiteSparse_SOURCE_DIR}/CXSparse/Include	
	# ${SuiteSparse_SOURCE_DIR}/UMFPACK/Include
	# ${SuiteSparse_SOURCE_DIR}/BTF/Include
	# ${SuiteSparse_SOURCE_DIR}/KLU/Include
	# CACHE INTERNAL "")

# include_directories(${SUITESPARSE_INCLUDE_DIRS})

include_directories(${SuiteSparse_SOURCE_DIR}/SuiteSparse_config)

macro(add_suit_library name target)
	file(GLOB hdrs "${name}/Include/*.h")
    file(GLOB srcs "${name}/SourceWrappers/*.c")
	
	include_directories(${name}/Include)
	
	add_library(${target}
		SuiteSparse_config/SuiteSparse_config.c
		SuiteSparse_config/SuiteSparse_config.h
		${hdrs} ${srcs})
endmacro()

add_suit_library(AMD amd)
add_suit_library(CAMD camd)
add_suit_library(COLAMD colamd)
add_suit_library(CCOLAMD ccolamd)
add_suit_library(CHOLMOD cholmod)
add_suit_library(CXSparse cxsparse)
add_suit_library(BTF btf)
add_suit_library(KLU klu)

IF (BUILD_METIS)
	add_dependencies(suitesparse metis)
ENDIF (BUILD_METIS)
