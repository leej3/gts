cmake_minimum_required(VERSION 3.1)

add_executable(predicates_init src/predicates_init.c)

execute_process(
  COMMAND predicates_init.exe
  OUTPUT_FILE predicates_init.h
  OUTPUT_VARIABLE OUTVAR)

MESSAGE("${OUTVAR}")

set(kit gts)
project(${kit})

set(srcs
  src/bbtree.c
  src/boolean.c
  src/cdt.c
  src/container.c
  src/curvature.c
  src/edge.c
  src/eheap.c
  src/face.c
  src/fifo.c
  src/graph.c
  src/heap.c
  src/hsurface.c
  src/iso.c
  src/isotetra.c
  src/kdtree.c
  src/matrix.c
  src/misc.c
  src/named.c
  src/object.c
  src/oocs.c
  src/partition.c
  src/pgraph.c
  src/point.c
  src/predicates.c
  src/psurface.c
  src/refine.c
  src/segment.c
  src/split.c
  src/stripe.c
  src/surface.c
  src/triangle.c
  src/tribox3.c
  src/vertex.c
  src/vopt.c
  )
if(MSVC)
  configure_file(src/config.h.win32 config.h)
else()
  configure_file(config.h.in config.h)
endif()

SET(CMAKE_INCLUDE_CURRENT_DIR ON)

include_directories(
  ${CMAKE_CURRENT_SOURCE_DIR}/src
  )
find_package(ZLIB REQUIRED)
find_package(unofficial-glib CONFIG REQUIRED)
set(glib_libraries_ "unofficial::glib::gio;unofficial::glib::glib;unofficial::glib::gmodule;unofficial::glib::gobject")


add_library(${kit} SHARED ${srcs})
target_compile_definitions(${kit} PRIVATE GTS_COMPILATION=1 )
target_link_libraries(${kit} ${glib_libraries_})

if(MSVC)
  SET_TARGET_PROPERTIES(${kit} PROPERTIES LINK_FLAGS "/DEF:\"${CMAKE_CURRENT_SOURCE_DIR}/src/gts.def\"")
  INSTALL(TARGETS ${kit}
    RUNTIME DESTINATION bin COMPONENT RuntimeLibraries
    LIBRARY DESTINATION lib COMPONENT RuntimeLibraries
    ARCHIVE DESTINATION lib COMPONENT Development
    )
else()
  INSTALL(TARGETS ${kit}
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    )
endif()

INSTALL(FILES
  src/gts.h
  src/gtsconfig.h
  DESTINATION include COMPONENT Development
  )
