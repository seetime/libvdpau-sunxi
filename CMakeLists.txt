cmake_minimum_required(VERSION 2.8)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_OUTPUT_DIR})

# set project name from current directory
set( TARGET vdpau-sunxi)
project( ${TARGET} )

set(BUILD_SHARED_LIBS YES)

SET(${TARGET}_HEADERS
  ${CMAKE_FIND_ROOT_PATH}/usr/local/include/vdpau/vdpau.h
  kernel-headers/drv_display.h
  kernel-headers/g2d_driver.h
  kernel-headers/sunxi_disp_ioctl.h
  kernel-headers/drv_display.h
  rgba.h
  rgba_g2d.h
  rgba_pixman.h
  sunxi_disp.h
  tiled_yuv.h
  vdpau_private.h
)

SET(${TARGET}_SOURCES 
	device.c
    presentation_queue.c
    surface_output.c
    surface_video.c
    surface_bitmap.c
    video_mixer.c
    decoder.c
    handles.c
    h264.c
    mpeg12.c
    mpeg4.c
    rgba.c
    tiled_yuv.S
    sunxi_disp.c
    sunxi_disp2.c
    sunxi_disp1_5.c
    rgba_g2d.c
    rgba_pixman.c
)

INCLUDE_DIRECTORIES(
    ./include/  
    ${EXTERN_PATH}
    ${PIXMAN_INCLUDE_DIR}
    ${CMAKE_FIND_ROOT_PATH}/usr/local/include
)


add_definitions(-DLIBDIR=\"/usr/local/lib\")

if (CMAKE_FIND_ROOT_PATH)
  # set the path to the library folder
  link_directories(
    #${CMAKE_LIBRARY_PATH}
  )

  SET( CMAKE_EXE_LINKER_FLAGS  "${CMAKE_EXE_LINKER_FLAGS} --sysroot=${CMAKE_FIND_ROOT_PATH}" )
else()
  #set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -mfloat-abi=soft")
  #set (CMAKE_CXX__FLAGS "${CMAKE_CXX__FLAGS} -mfloat-abi=soft")  
endif()

set (PC_PIXMAN_LIBDIR "${CMAKE_FIND_ROOT_PATH}/usr -mfloat-abi=soft")

find_package(X11 REQUIRED)
find_package(Pixman REQUIRED)

INCLUDE_DIRECTORIES(
    ${PIXMAN_INCLUDE_DIR}
)

#MESSAGE ("@${TARGET} -- ${ENV{LIBDIR}")
MESSAGE ("@${TARGET} -- PIXMAN_INCLUDE_DIR : ${PIXMAN_INCLUDE_DIR}") 
MESSAGE ("@${TARGET} -- PIXMAN_LIBRARY : ${PIXMAN_LIBRARY}") 

set (CMAKE_C_FLAGS ${CMAKE_C_FLAGS} 
    "-fpic -fvisibility=hidden -Wall -O3"
)
set (CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} 
    "-fpic -fvisibility=hidden -Wall -O3"
)

IF (UNIX)
  set(MORELIBS cedrus ${X11_LIBRARIES} m rt)
ENDIF()



ADD_LIBRARY( ${TARGET} ${${TARGET}_SOURCES} ${${TARGET}_HEADERS} )

TARGET_LINK_LIBRARIES( ${TARGET}  ${MORELIBS} )
