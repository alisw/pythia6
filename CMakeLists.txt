Cmake_Minimum_Required(VERSION 2.6.0 FATAL_ERROR)

Project(Pythia6)
Enable_Language(Fortran)


set(Pythia_FSRCS
pythia-6.4.21.f
tpythia6_called_from_cc.F
)


# The order here is important because gfortran is contained in pgfortran
If (CMAKE_Fortran_COMPILER MATCHES "gfortran")
#  set(UnderScoreString "-fno-second_underscore")
EndIf (CMAKE_Fortran_COMPILER MATCHES "gfortran")
If (CMAKE_Fortran_COMPILER MATCHES "pgfortran")
  set(UnderScoreString "-Mnosecond_underscore")
EndIf (CMAKE_Fortran_COMPILER MATCHES "pgfortran")


#Set_Source_Files_Properties(${Pythia_FSRCS} PROPERTIES COMPILE_FLAGS
#                           ${UnderScoreString})

# create c source
Execute_Process(COMMAND echo void MAIN() {} OUTPUT_FILE main.c)

set(Pythia_CSRCS
pythia6_common_address.c
main.c
)

# using Clang on Mac OSX
if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang" AND APPLE)
  set(CMAKE_SHARED_LINKER_FLAGS
    "${CMAKE_SHARED_LINKER_FLAGS} -undefined dynamic_lookup -Wl,-no_compact_unwind")
endif()

Add_Library(pythia6 SHARED ${Pythia_FSRCS} ${Pythia_CSRCS})
If (CMAKE_Fortran_COMPILER MATCHES "gfortran")
Target_Link_Libraries(pythia6 gfortran)
EndIf (CMAKE_Fortran_COMPILER MATCHES "gfortran")
Install(TARGETS pythia6 DESTINATION LIBRARY DESTINATION lib)
