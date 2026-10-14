cmake_minimum_required(VERSION 3.22)

project(triorb_mj_description VERSION 1.0.0)

option(HONOR_INSTALL_PREFIX
       "Whether to install in CMAKE_INSTALL_PREFIX (for nix)" OFF)
option(SRC_MODE "Use files in the repository instead of installed files" OFF)

if(HONOR_INSTALL_PREFIX)
  set(MC_MUJOCO_SHARE_DESTINATION "${CMAKE_INSTALL_PREFIX}")
endif()

# Generate XML with resolved mesh paths
configure_file(xml/triorb.xml "${CMAKE_CURRENT_BINARY_DIR}/xml/triorb.xml")

set(triorb_XML_MODEL_PATH "xml/triorb.xml")
set(triorb_PDGAINS_PATH "pdgains/PDgains_sim.dat")

if(SRC_MODE)
  set(triorb_XML_MODEL_PATH
      "${CMAKE_CURRENT_BINARY_DIR}/${triorb_XML_MODEL_PATH}")
  set(triorb_PDGAINS_PATH "${CMAKE_CURRENT_SOURCE_DIR}/${triorb_PDGAINS_PATH}")
else()
  set(triorb_XML_MODEL_PATH
      "${MC_MUJOCO_SHARE_DESTINATION}/triorb/${triorb_XML_MODEL_PATH}")
  set(triorb_PDGAINS_PATH
      "${MC_MUJOCO_SHARE_DESTINATION}/triorb/${triorb_PDGAINS_PATH}")
endif()

configure_file(mc_mujoco/triorb.in.yaml
               "${CMAKE_CURRENT_BINARY_DIR}/mc_mujoco/triorb.yaml")
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/mc_mujoco/triorb.yaml"
        DESTINATION "${MC_MUJOCO_SHARE_DESTINATION}")

install(
  DIRECTORY pdgains
  DESTINATION "${MC_MUJOCO_SHARE_DESTINATION}/triorb"
  FILES_MATCHING
  PATTERN "*.dat")

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/xml/triorb.xml"
        DESTINATION "${MC_MUJOCO_SHARE_DESTINATION}/triorb/xml")