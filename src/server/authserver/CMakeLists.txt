# Copyright (C) 2008-2010 Trinity <http://www.trinitycore.org/>
#
# This file is free software; as a special exception the author gives
# unlimited permission to copy and/or distribute it, with or without
# modifications, as long as this notice is preserved.
#
# This program is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY, to the extent permitted by law; without even the
# implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

########### authserver ###############

set(authserver_SRCS
  Authentication/AuthCodes.cpp
  Realms/RealmList.cpp
  Server/AuthSocket.cpp
  Server/RealmSocket.cpp
  Main.cpp
)

include_directories(
  ${ACE_INCLUDE_DIR}
  ${MYSQL_INCLUDE_DIR}
  ${OPENSSL_INCLUDE_DIR}
  ${CMAKE_BINARY_DIR}
  ${CMAKE_SOURCE_DIR}/src/server/shared
  ${CMAKE_SOURCE_DIR}/src/server/shared/Database
  ${CMAKE_SOURCE_DIR}/src/server/shared/Packets
  ${CMAKE_SOURCE_DIR}/src/server/shared/Cryptography
  ${CMAKE_SOURCE_DIR}/src/server/shared/Cryptography/Authentication
  ${CMAKE_SOURCE_DIR}/src/server/shared/Logging
  ${CMAKE_SOURCE_DIR}/src/server/shared/Utilities
  ${CMAKE_SOURCE_DIR}/src/server/authserver
  ${CMAKE_SOURCE_DIR}/src/server/authserver/Authentication
  ${CMAKE_SOURCE_DIR}/src/server/authserver/Realms
  ${CMAKE_SOURCE_DIR}/src/server/authserver/Server
)

set(authserver_LINK_FLAGS "")

add_executable(authserver
  ${authserver_SRCS}
)

add_dependencies(authserver revision.h)

if( NOT WIN32 )
  add_definitions(-D_TRINITY_REALM_CONFIG='"${CONF_DIR}/authserver.conf"')
endif()

if( UNIX )
  set(authserver_LINK_FLAGS "-pthread ${authserver_LINK_FLAGS}")
endif()

if( CMAKE_SYSTEM_NAME MATCHES "Darwin" )
  set(authserver_LINK_FLAGS "-framework Carbon ${authserver_LINK_FLAGS}")
endif()

set_target_properties(authserver PROPERTIES LINK_FLAGS "${authserver_LINK_FLAGS}")

if( WIN32 )
  target_link_libraries(
    authserver
    shared
    trinitydatabase
    trinityauth
    trinityconfig
    ${MYSQL_LIBRARY}
    ${OPENSSL_LIBRARIES}
    ${OPENSSL_EXTRA_LIBRARIES}
  )
else()
  target_link_libraries(
    authserver
    shared
    trinitydatabase
    trinityauth
    trinityconfig
    ${MYSQL_LIBRARY}
    ${OPENSSL_LIBRARIES}
    ${OSX_LIBS}
  )
endif()

if( UNIX )
  install(TARGETS authserver DESTINATION bin)
  install(FILES  authserver.conf.dist DESTINATION etc)
endif()
