find_package(PCL 1.6 REQUIRED COMPONENTS common io visualization)

IF(WINDOWS)
#CMAKE_POLICY(SET CMP0028 OLD)
FIND_PACKAGE(Qt5Widgets REQUIRED)
ENDIF()

add_definitions(${PCL_DEFINITIONS})

add_library(voxelpcl SHARED PCLGrabber.cpp)
target_include_directories(voxelpcl PUBLIC ${PCL_INCLUDE_DIRS} ${EIGEN_INCLUDE_DIRS})
target_link_libraries(voxelpcl LINK_PUBLIC voxel LINK_PRIVATE ${PCL_COMMON_LIBRARIES} ${PCL_IO_LIBRARIES} ${PCL_VISUALIZATION_LIBRARIES})

generate_export_header(voxelpcl
  EXPORT_FILE_NAME "VoxelPCLExports.h"
)

install(TARGETS voxelpcl
  EXPORT VoxelPCLTargets
  LIBRARY DESTINATION lib COMPONENT pcl
  ARCHIVE DESTINATION lib COMPONENT pcl
  RUNTIME DESTINATION lib COMPONENT pcl
)

install(FILES
  PCLGrabber.h
  ${CMAKE_CURRENT_BINARY_DIR}/VoxelPCLExports.h
  DESTINATION include/voxel/pcl
  COMPONENT pcl_dev
)

set_target_properties(voxelpcl PROPERTIES 
                      VERSION ${VOXEL_PCL_VERSION} 
                      SOVERSION ${VOXEL_PCL_ABI_VERSION})


IF(LINUX)
  set(CPACK_COMPONENTS_ALL pcl)
  set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "PCL integration with Grabber and Viewer")
  set(CPACK_DEBIAN_PACKAGE_DEPENDS "libvoxel (>= ${VOXEL_VERSION}), libpcl-visualization-1.7, libpcl-io-1.7, libpcl-common-1.7")
  create_cpack_config(libvoxel-pcl ${VOXEL_PCL_VERSION})

  set(CPACK_COMPONENTS_ALL pcl_dev)
  set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Voxel library developer package. Contains header files needed to compile applications linking to Voxel library.")
  set(CPACK_DEBIAN_PACKAGE_DEPENDS "libvoxel-dev (>= ${VOXEL_VERSION}), libpcl-visualization-1.7-dev, libpcl-io-1.7-dev, libpcl-common-1.7-dev")
  create_cpack_config(libvoxel-pcl-dev ${VOXEL_PCL_VERSION})
ENDIF()
