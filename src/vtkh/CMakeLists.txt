#==============================================================================
# See License.txt
#==============================================================================
add_subdirectory(utils)
add_subdirectory(thirdparty_builtin)

set(vtkh_core_headers
  DataSet.hpp
  Error.hpp
  vtkh.hpp
  )

set(vtkh_core_sources
  DataSet.cpp
  vtkh.cpp
  )


set(vtkh_core_deps vtkm diy vtkh_utils)


if(CUDA_FOUND)
  list(APPEND vtkh_core_deps cuda)
endif()

if(ENABLE_OPENMP)
    list(APPEND vtkm_core_deps openmp)
endif()


# blt vtk dep, includes all of vtkm
blt_add_library(
  NAME vtkh_core
  SOURCES ${vtkh_core_sources}
  HEADERS ${vtkh_core_headers}
  DEPENDS_ON ${vtkh_core_deps}
  )

if(CUDA_FOUND AND NOT ENABLE_SHARED_LIBS)
  set_target_properties(vtkh_core PROPERTIES CUDA_RESOLVE_DEVICE_SYMBOLS ON)
endif()

# Install libraries
install(TARGETS vtkh_core
  EXPORT ${VTKh_EXPORT_NAME}
  ARCHIVE DESTINATION ${VTKh_INSTALL_LIB_DIR}
  LIBRARY DESTINATION ${VTKh_INSTALL_LIB_DIR}
  RUNTIME DESTINATION ${VTKh_INSTALL_BIN_DIR}
)

# Install headers
install(FILES ${vtkh_core_headers}
  DESTINATION ${VTKh_INSTALL_INCLUDE_DIR}/vtkh)

if(MPI_FOUND)

  set(vtkh_core_mpi_deps vtkm diy vtkh_utils_mpi)

  if(CUDA_FOUND)
    list(APPEND vtkh_core_mpi_deps cuda)
  endif()


  blt_add_library(
      NAME vtkh_core_mpi
      SOURCES ${vtkh_core_sources}
      HEADERS ${vtkh_core_headers}
      DEPENDS_ON ${vtkh_core_mpi_deps} mpi
      )

if(CUDA_FOUND AND NOT ENABLE_SHARED_LIBS)
  set_target_properties(vtkh_core_mpi PROPERTIES CUDA_RESOLVE_DEVICE_SYMBOLS ON)
endif()
 
  blt_add_target_compile_flags(TO vtkh_core_mpi FLAGS "-DVTKH_PARALLEL")

  # Install libraries
  install(TARGETS vtkh_core_mpi
    EXPORT ${VTKh_EXPORT_NAME}
    ARCHIVE DESTINATION ${VTKh_INSTALL_LIB_DIR}
    LIBRARY DESTINATION ${VTKh_INSTALL_LIB_DIR}
    RUNTIME DESTINATION ${VTKh_INSTALL_BIN_DIR}
    )
endif()

add_subdirectory(vtkm_filters)
add_subdirectory(filters)
add_subdirectory(rendering)
