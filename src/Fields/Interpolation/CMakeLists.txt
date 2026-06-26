set (_SRCS
    Mesh.cpp
    NDGrid.cpp
    )

set (HDRS
    Mesh.h
    NDGrid.cpp
    )

target_include_directories(opalx PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

target_sources(opalx PRIVATE ${_SRCS})

install (FILES ${HDRS} DESTINATION "${CMAKE_INSTALL_PREFIX}/include/Interpolation")