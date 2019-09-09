# Include generated *.pb.h files
include_directories(${INCLUDE_DIRS} ${GRPC_GENERATED})

# source code
if (ROUTE)
  set(CLIENT_SOURCES src/route_guide/route_guide_client.cc)
else ()
  set(CLIENT_SOURCES src/greeter/greeter_client.cc)
endif ()

# Append generated *.pb.cc files for both server executable and client executable
list(APPEND CLIENT_SOURCES ${hw_proto_srcs})
list(APPEND CLIENT_SOURCES ${hw_grpc_srcs})

if (ROUTE)
  list(APPEND CLIENT_SOURCES src/route_guide/helper.cc)
endif ()

# target
if (ROUTE)
  add_executable(client_route ${CLIENT_SOURCES})
  target_link_libraries(client_route ${LINK_LIBS})
else ()
  add_executable(client_greeter ${CLIENT_SOURCES})
  target_link_libraries(client_greeter ${LINK_LIBS})
endif ()
