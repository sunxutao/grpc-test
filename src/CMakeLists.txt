# Proto file
if (ROUTE)
  get_filename_component(proto "${PROJECT_SOURCE_DIR}/protos/route_guide.proto" ABSOLUTE)
else ()
  get_filename_component(proto "${PROJECT_SOURCE_DIR}/protos/helloworld.proto" ABSOLUTE)
endif ()
get_filename_component(proto_path "${proto}" PATH)

# Generated sources
execute_process(COMMAND mkdir -p "${CMAKE_BINARY_DIR}/protobuf_output")
set(GRPC_GENERATED "${CMAKE_BINARY_DIR}/protobuf_output")
if (ROUTE)
  set(proto_srcs "${GRPC_GENERATED}/route_guide.pb.cc")
  set(proto_hdrs "${GRPC_GENERATED}/route_guide.pb.h")
  set(grpc_srcs "${GRPC_GENERATED}/route_guide.grpc.pb.cc")
  set(grpc_hdrs "${GRPC_GENERATED}/route_guide.grpc.pb.h")
else ()
  set(proto_srcs "${GRPC_GENERATED}/helloworld.pb.cc")
  set(proto_hdrs "${GRPC_GENERATED}/helloworld.pb.h")
  set(grpc_srcs "${GRPC_GENERATED}/helloworld.grpc.pb.cc")
  set(grpc_hdrs "${GRPC_GENERATED}/helloworld.grpc.pb.h")
endif ()

add_custom_command(
  OUTPUT "${proto_srcs}" "${proto_hdrs}" "${grpc_srcs}" "${grpc_hdrs}"
  COMMAND "${_PROTOBUF_PROTOC}"
  ARGS --grpc_out "${GRPC_GENERATED}"
    --cpp_out "${GRPC_GENERATED}"
    -I "${proto_path}"
    --plugin=protoc-gen-grpc="${_GRPC_CPP_PLUGIN_EXECUTABLE}"
    "${proto}"
  DEPENDS "${proto}"
)

# Include generated *.pb.h files
include_directories(${GRPC_GENERATED})

# Append generated *.pb.cc files for both server executable and client executable
list(APPEND CLIENT_SOURCES ${proto_srcs})
list(APPEND CLIENT_SOURCES ${grpc_srcs})
list(APPEND SERVER_SOURCES ${proto_srcs})
list(APPEND SERVER_SOURCES ${grpc_srcs})

# source code
if (ROUTE)
  aux_source_directory(client/route_guide ROUTE_CLIENT)
  list(APPEND CLIENT_SOURCES ${ROUTE_CLIENT})
  aux_source_directory(server/route_guide ROUTE_SERVER)
  list(APPEND SERVER_SOURCES ${ROUTE_SERVER})
else ()
  aux_source_directory(client/greeter GREETER_CLIENT)
  list(APPEND CLIENT_SOURCES ${GREETER_CLIENT})
  aux_source_directory(server/greeter GREETER_SERVER)
  list(APPEND SERVER_SOURCES ${GREETER_SERVER})
endif ()

# target
if (ROUTE)
  add_executable(client_route ${CLIENT_SOURCES})
  target_link_libraries(client_route ${LINK_LIBS})
  add_executable(server_route ${SERVER_SOURCES})
  target_link_libraries(server_route ${LINK_LIBS})
else ()
  add_executable(client_greeter ${CLIENT_SOURCES})
  target_link_libraries(client_greeter ${LINK_LIBS})
  add_executable(server_greeter ${SERVER_SOURCES})
  target_link_libraries(server_greeter ${LINK_LIBS})
endif ()
