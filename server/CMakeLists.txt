# Include generated *.pb.h files
include_directories(${INCLUDE_DIRS} ${GRPC_GENERATED})

# source code
if (ROUTE)
  set(SERVER_SOURCES src/route_guide/route_guide_server.cc)
else ()
  set(SERVER_SOURCES src/greeter/greeter_server.cc)
endif ()

# Append generated *.pb.cc files for both server executable and client executable
list(APPEND SERVER_SOURCES ${hw_proto_srcs})
list(APPEND SERVER_SOURCES ${hw_grpc_srcs})

if (ROUTE)
  list(APPEND SERVER_SOURCES src/route_guide/helper.cc)
endif ()

# target
if (ROUTE)
  add_executable(server_route ${SERVER_SOURCES})
  target_link_libraries(server_route ${LINK_LIBS})
else ()
  add_executable(server_greeter ${SERVER_SOURCES})
  target_link_libraries(server_greeter ${LINK_LIBS})
endif ()
