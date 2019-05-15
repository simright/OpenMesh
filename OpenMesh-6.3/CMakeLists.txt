cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project (OpenMeshCore)

macro (acg_append_files ret ext)
  foreach (_dir ${ARGN})
    file (GLOB _files "${_dir}/${ext}")
    foreach (_file ${_files})
      get_filename_component (_filename ${_file} NAME)
      if (_filename MATCHES "^[.]")
	list (REMOVE_ITEM _files ${_file})
      endif ()
    endforeach ()
    list (APPEND ${ret} ${_files})
  endforeach ()
endmacro ()

# drop all "*T.cc" files from list
macro (acg_drop_templates list)
  foreach (_file ${${list}})
    if (_file MATCHES "T.cc$")
      list (REMOVE_ITEM ${list} ${_file})
    endif ()
  endforeach ()
endmacro ()

include_directories (
  ${CMAKE_CURRENT_SOURCE_DIR}
)

# source code directories
set (directories
  OpenMesh/Core/Geometry 
  OpenMesh/Core/IO 
  OpenMesh/Core/IO/exporter 
  OpenMesh/Core/IO/importer 
  OpenMesh/Core/IO/reader 
  OpenMesh/Core/IO/writer
  OpenMesh/Core/Mesh 
  OpenMesh/Core/Mesh/gen 
  OpenMesh/Core/System 
  OpenMesh/Core/Utils
  OpenMesh/Tools/Decimater
  OpenMesh/Tools/Dualizer
  OpenMesh/Tools/Smoother
  OpenMesh/Tools/Subdivider/Adaptive/Composite
  OpenMesh/Tools/Subdivider/Uniform/Composite 
  OpenMesh/Tools/Subdivider/Uniform
  OpenMesh/Tools/Utils
  OpenMesh/Tools/VDPM
)

# collect all header and source files
acg_append_files (headers "*.hh" ${directories})
acg_append_files (sources "*.cc" ${directories})

#Drop the template only cc files
acg_drop_templates(sources)


# Disable Library installation when not building OpenMesh on its own but as part of another project!
if ( NOT ${PROJECT_NAME} MATCHES "OpenMesh")
  set(ACG_NO_LIBRARY_INSTALL true)
endif()


if (MSVC)
  add_definitions(-D_USE_MATH_DEFINES)
elseif(UNIX)
  add_definitions(-DLINUX)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
endif ()

add_library (OpenMeshCore STATIC ${sources} ${headers})







