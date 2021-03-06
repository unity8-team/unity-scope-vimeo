
# Put the ini file in the build directory next to the scope
# .so file so test tools can find both easily.
intltool_merge_translations(
  "${CMAKE_SOURCE_DIR}/data/${SCOPE_NAME}.ini.in"
  "${CMAKE_CURRENT_BINARY_DIR}/${SCOPE_INSTALL_NAME}.ini"
  ALL
  UTF8
)
configure_file(
  "${CMAKE_SOURCE_DIR}/data/logo.png"
  "${CMAKE_CURRENT_BINARY_DIR}/logo.png"
  @ONLY
  COPYONLY
)

add_library(
  ${SCOPE_NAME}-static OBJECT
  vimeo/api/channel.cpp
  vimeo/api/client.cpp
  vimeo/api/login.cpp
  vimeo/api/video.cpp
  vimeo/scope/preview.cpp
  vimeo/scope/query.cpp
  vimeo/scope/scope.cpp
)

set_target_properties(
  ${SCOPE_NAME}-static
  PROPERTIES
    LINK_FLAGS "-Wl,--export-all-symbols"
)

add_library(
  ${SCOPE_NAME} SHARED
  $<TARGET_OBJECTS:${SCOPE_NAME}-static>
)

target_link_libraries(
  ${SCOPE_NAME}
  ${SCOPE_LDFLAGS}
  ${Boost_LIBRARIES}
  ${CMAKE_THREAD_LIBS_INIT}
)

set_target_properties(
  ${SCOPE_NAME}
  PROPERTIES
    LINK_FLAGS "-Wl,--no-undefined -Wl,--version-script,${SYMBOL_MAP}"
    LINK_DEPENDS "${SYMBOL_MAP}"
    OUTPUT_NAME ${SCOPE_INSTALL_NAME}
)

install(
  TARGETS ${SCOPE_NAME}
  LIBRARY DESTINATION ${SCOPE_INSTALL_DIR}
)
