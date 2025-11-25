cmake_minimum_required(VERSION 3.27.1 FATAL_ERROR)
project(Pythia6)
enable_language(Fortran)
set(PYTHIA_FSRCS pythia-6.4.28.f tpythia6_called_from_cc.F)

# The order here is important because gfortran is contained in pgfortran
if(CMAKE_Fortran_COMPILER MATCHES "gfortran")
#  set(UNDERSCORESTRING "-fno-second_underscore")
endif()
if(CMAKE_Fortran_COMPILER MATCHES "pgfortran")
  set(UNDERSCORESTRING "-Mnosecond_underscore")
endif()


#Set_Source_Files_Properties(${PYTHIA_FSRCS} PROPERTIES COMPILE_FLAGS
#                           ${UNDERSCORESTRING})

# create c source
execute_process(COMMAND echo void MAIN() {} OUTPUT_FILE main.c)

set(PYTHIA_CSRCS
pythia6_common_address.c
main.c
)

# using Clang on Mac OSX
if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang" AND APPLE)
  set(CMAKE_SHARED_LINKER_FLAGS
    "${CMAKE_SHARED_LINKER_FLAGS} -undefined dynamic_lookup -Wl,-no_compact_unwind")
endif()

add_library(pythia6 SHARED ${PYTHIA_FSRCS} ${PYTHIA_CSRCS})
if(CMAKE_Fortran_COMPILER MATCHES "gfortran")
target_link_libraries(pythia6 gfortran)
endif()
install(TARGETS pythia6 DESTINATION LIBRARY DESTINATION lib)
