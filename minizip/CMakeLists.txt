CMAKE_MINIMUM_REQUIRED(VERSION 3.5 FATAL_ERROR)

PROJECT(minizip LANGUAGES C)

SET(MINIZIP_PUBLIC_HEADERS
  ${MINIZIP_SOURCE_DIR}/zip.h
  ${MINIZIP_SOURCE_DIR}/unzip.h
)

SET(MINIZIP_PRIVATE_HEADERS
  ${MINIZIP_SOURCE_DIR}/crypt.h
  ${MINIZIP_SOURCE_DIR}/ioapi.h
  ${MINIZIP_SOURCE_DIR}/ioapi_buf.h
  ${MINIZIP_SOURCE_DIR}/ioapi_mem.h

)

SET(MINIZIP_SRCS
  ${MINIZIP_SOURCE_DIR}/crypt.c
  ${MINIZIP_SOURCE_DIR}/ioapi.c
  ${MINIZIP_SOURCE_DIR}/ioapi_buf.c
  ${MINIZIP_SOURCE_DIR}/ioapi_mem.c
  ${MINIZIP_SOURCE_DIR}/unzip.c
  ${MINIZIP_SOURCE_DIR}/zip.c
)

IF(WIN32)
  list(APPEND MINIZIP_SRCS ${MINIZIP_SOURCE_DIR}/iowin32.c)
  list(APPEND MINIZIP_PRIVATE_HEADERS ${MINIZIP_SOURCE_DIR}/iowin32.h)
ENDIF()

IF(MSVC)
  LIST(APPEND PRIVATE_DEFINITIONS -D_CRT_SECURE_NO_WARNINGS) # Disable warnings for unsecure CRT functions
  
  SET(CMAKE_DEBUG_POSTFIX "d")
ENDIF(MSVC)

if(UNIX)
  LIST(APPEND PRIVATE_OPTIONS -fPIC)
  LIST(APPEND PRIVATE_OPTIONS -O3)
  LIST(APPEND PRIVATE_DEFINITIONS -D__USE_FILE_OFFSET64)
  LIST(APPEND PRIVATE_DEFINITIONS -D__USE_LARGEFILE64)
  LIST(APPEND PRIVATE_DEFINITIONS -D_LARGEFILE64_SOURCE)
  LIST(APPEND PRIVATE_DEFINITIONS -D_FILE_OFFSET_BIT=64)
endif()

ADD_LIBRARY(minizip STATIC ${MINIZIP_SRCS} ${MINIZIP_PUBLIC_HEADERS} ${MINIZIP_PRIVATE_HEADERS})

TARGET_INCLUDE_DIRECTORIES(minizip PUBLIC 
  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}> 
  $<BUILD_INTERFACE:${MINIZIP_SOURCE_DIR}/include> 
  $<INSTALL_INTERFACE:include>
  )

TARGET_COMPILE_DEFINITIONS(minizip
  PRIVATE ${PRIVATE_DEFINITIONS}
  )
  
TARGET_COMPILE_OPTIONS(minizip
  PRIVATE ${PRIVATE_OPTIONS}
  )

TARGET_LINK_LIBRARIES(minizip zlib)

# INSTALL
INSTALL(TARGETS minizip EXPORT minizip_export
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
  )

INSTALL(FILES 
  ${MINIZIP_PUBLIC_HEADERS} 
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
  )
