cmake_minimum_required(VERSION 2.6)

SET(TARGET DistributedServer)
	
PROJECT(${TARGET})

# Workaround for bug 0003353 in cmake (aparently not fixed)
SET_SOURCE_FILES_PROPERTIES(${PB_PLUGIN} PROPERTIES GENERATED TRUE)

SET(SRCS
	"${TARGET}.cpp"
	"handler_impl.cpp"
	${NSCP_INCLUDEDIR}/nscp/packet.cpp
	${NSCP_INCLUDEDIR}/nscp/handler.cpp

	${NSCP_DEF_PLUGIN_CPP}
)


IF(WIN32)
	SET(SRCS ${SRCS}
		${TARGET}.h
		handler_impl.hpp
		queue_manager.hpp
		worker_manager.hpp
		${NSCP_INCLUDEDIR}/nscp/packet.hpp
		${NSCP_INCLUDEDIR}/nscp/handler.hpp

		${NSCP_DEF_PLUGIN_HPP}
	)
	SET(EXTRA_LIB Rpcrt4.lib Ws2_32.lib)
	ADD_DEFINITIONS(-DSTATIC_LIB)
ENDIF(WIN32)

ADD_DEFINITIONS(${NSCP_GLOBAL_DEFINES})
ADD_LIBRARY(${TARGET} MODULE ${SRCS})
INCLUDE_DIRECTORIES(${ZEROMQ_INCLUDE_DIR})

target_link_libraries(${TARGET}
	${Boost_FILESYSTEM_LIBRARY}
	${Boost_THREAD_LIBRARY}
	${NSCP_DEF_PLUGIN_LIB}
	${ZEROMQ_LIBRARY}
	${EXTRA_LIB}
)

INCLUDE(${BUILD_CMAKE_FOLDER}/module.cmake)
SOURCE_GROUP("Server" REGULAR_EXPRESSION .*include/nscp/.*)
