
add_executable(predicates_init src/predicates_init.c)

execute_process(
  COMMAND predicates_init.exe
  OUTPUT_FILE predicates_init.h
  OUTPUT_VARIABLE OUTVAR)

MESSAGE("${OUTVAR}")

set(kit gts)
project(${kit})
include(FindThreads)

set(srcs
  bbtree.c
  boolean.c
  cdt.c
  container.c
  curvature.c
  edge.c
  eheap.c
  face.c
  fifo.c
  graph.c
  heap.c
  hsurface.c
  iso.c
  isotetra.c
  kdtree.c
  matrix.c
  misc.c
  named.c
  object.c
  oocs.c
  partition.c
  pgraph.c
  point.c
  predicates.c
  psurface.c
  refine.c
  segment.c
  split.c
  stripe.c
  surface.c
  triangle.c
  tribox3.c
  vertex.c
  vopt.c
  )
if(MSVC)
  configure_file(config.h.win32 config.h)
else()
  configure_file(config.h.in config.h)
  configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/../config.h.in 
    ${CMAKE_CURRENT_BINARY_DIR}/src/config.h
    )
endif()


include_directories(
  ${CMAKE_CURRENT_SOURCE_DIR}/src
  )


add_library(${kit} SHARED ${srcs})
target_compile_definitions(${kit} PRIVATE GTS_COMPILATION=1 )
target_include_directories(${kit} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/src>
  $<INSTALL_INTERFACE:include>
  )
target_link_libraries(${kit} GLib2::glib)

if(MSVC)
  SET_TARGET_PROPERTIES(${kit} PROPERTIES LINK_FLAGS "/DEF:\"${CMAKE_CURRENT_SOURCE_DIR}/gts.def\"")
  INSTALL(TARGETS ${kit}
  EXPORT ${kit}-export
    RUNTIME DESTINATION bin COMPONENT RuntimeLibraries
    LIBRARY DESTINATION lib COMPONENT RuntimeLibraries
    ARCHIVE DESTINATION lib COMPONENT Development
    )
else()
  INSTALL(TARGETS ${kit}
   EXPORT ${kit}-export
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    )
endif()

install(EXPORT ${kit}-export
FILE
${kit}Targets.cmake
NAMESPACE
gts::
DESTINATION
share
)
configure_file(${PROJECT_NAME}-config.cmake.in ${PROJECT_NAME}-config.cmake @ONLY)
install(FILES ${CMAKE_BINARY_DIR}/${PROJECT_NAME}-config.cmake DESTINATION share/)
install(FILES
  src/gts.h
  src/gtsconfig.h
  DESTINATION include COMPONENT Development
  )
