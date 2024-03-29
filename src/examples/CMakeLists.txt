FIND_PACKAGE(Boost REQUIRED COMPONENTS program_options)

IF(NEMO_MPI_ENABLED)
FIND_PACKAGE(CUDA)
    INCLUDE_DIRECTORIES(
    	${CMAKE_SOURCE_DIR}/src
    	${CMAKE_BINARY_DIR}/src
    	${CMAKE_SOURCE_DIR}/src/mpi
    	${CMAKE_SOURCE_DIR}/src/examples
    	${Boost_INCLUDE_DIRS}
    	${CUDA_INCLUDE_DIRS}
    )
ELSE(NEMO_MPI_ENABLED)
    INCLUDE_DIRECTORIES(
    	${CMAKE_SOURCE_DIR}/src
    	${CMAKE_BINARY_DIR}/src
    	${Boost_INCLUDE_DIRS}
    )
ENDIF(NEMO_MPI_ENABLED)

ADD_DEFINITIONS(-DUSING_MAIN)
LINK_DIRECTORIES(${Boost_LIBRARY_DIRS})

LINK_STATIC_MSVCR()

SET(INSTALL_FILES torus.cpp random.cpp common.cpp randomNetUtils.cpp)

IF(NEMO_MPI_ENABLED)
    LIST(APPEND INSTALL_FILES mpi_example.cpp)
    ADD_EXECUTABLE(mpi_example mpi_example.cpp randomNetUtils.cpp)
    ADD_DEPENDENCIES(mpi_example nemo_mpi)
    TARGET_LINK_LIBRARIES(mpi_example nemo_mpi)
ENDIF(NEMO_MPI_ENABLED)

ADD_EXECUTABLE(torus torus.cpp common.cpp)
TARGET_LINK_LIBRARIES(torus nemo ${Boost_LIBRARIES})

ADD_EXECUTABLE(random_example random.cpp randomNetUtils.cpp common.cpp)
TARGET_LINK_LIBRARIES(random_example nemo ${Boost_LIBRARIES})

ADD_EXECUTABLE(kuramoto kuramoto.cpp common.cpp)
TARGET_LINK_LIBRARIES(kuramoto nemo ${Boost_LIBRARIES})

IF(UNIX)
	SET(INSTALL_EXAMPLE_SRC_DIR "share/nemo/examples")
	SET(INSTALL_EXAMPLE_BIN_DIR "share/nemo/examples")
ENDIF(UNIX)

IF(WIN32)
	SET(INSTALL_EXAMPLE_SRC_DIR "examples")
	SET(INSTALL_EXAMPLE_BIN_DIR "examples")
ENDIF(WIN32)

#INSTALL(FILES ${INSTALL_FILES} DESTINATION ${CMAKE_SOURCE_DIR}/src)

IF(NEMO_MPI_ENABLED)
    INSTALL(TARGETS mpi_example random_example torus kuramoto DESTINATION ${INSTALL_EXAMPLE_BIN_DIR})
ELSE(NEMO_MPI_ENABLED)
    INSTALL(TARGETS random_example torus kuramoto DESTINATION ${INSTALL_EXAMPLE_BIN_DIR})
ENDIF(NEMO_MPI_ENABLED)
