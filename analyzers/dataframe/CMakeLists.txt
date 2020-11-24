
find_package(ROOT)
include(${ROOT_USE_FILE})


find_package(fccedm)
find_package(EDM4HEP)
find_package(podio)

include_directories(
        ${CMAKE_SOURCE_DIR}/analyzers/dataframe
        "${FCCEDM_INCLUDE_DIRS}"
        )

file(GLOB sources Re*.cc MC*.cc Al*.cc)
file(GLOB headers Re*.h  MC*.h Al*.h)

ROOT_GENERATE_DICTIONARY(G__FCCAnalyses MCParticle.h ReconstructedParticle.h ReconstructedParticle2Track.h ReconstructedParticle2MC.h Algorithm.h LINKDEF LinkDef.h)

add_library(FCCAnalyses SHARED ${sources} ${headers} G__FCCAnalyses.cxx )
target_link_libraries(FCCAnalyses ${FCCEDM_LIBRARIES} ROOT::Physics EDM4HEP::edm4hep podio::podio)

set_target_properties(FCCAnalyses PROPERTIES
  PUBLIC_HEADER "${headers}")

install(TARGETS FCCAnalyses
    RUNTIME DESTINATION "${INSTALL_BIN_DIR}" COMPONENT bin
    LIBRARY DESTINATION "${INSTALL_LIB_DIR}" COMPONENT shlib
    PUBLIC_HEADER DESTINATION "${INSTALL_INCLUDE_DIR}"
    COMPONENT dev
    )

install(FILES
  "${PROJECT_BINARY_DIR}/analyzers/dataframe/libFCCAnalyses.rootmap"
    DESTINATION "${INSTALL_LIB_DIR}" COMPONENT dev)

if (${ROOT_VERSION} GREATER 6)
    install(FILES
          "${PROJECT_BINARY_DIR}/analyzers/dataframe/libFCCAnalyses_rdict.pcm"
                DESTINATION "${INSTALL_LIB_DIR}" COMPONENT dev)
    endif()