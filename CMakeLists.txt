cmake_minimum_required(VERSION 3.16)
project(lowland-port-main)


add_subdirectory(assimp)


string(TOLOWER ${CMAKE_SYSTEM_NAME} LOWERED_SYSTEM_NAME)
string(TOLOWER ${CMAKE_SYSTEM_PROCESSOR} LOWERED_ARCH)
set(SYS_ARCH ${LOWERED_ARCH})

if(${LOWERED_ARCH} STREQUAL "amd64")
    set(SYS_ARCH "x64")
endif()

set(OUTPUT_DIR "${CMAKE_CURRENT_LIST_DIR}/../operating_system/storage-${LOWERED_SYSTEM_NAME}/${SYS_ARCH}")


message("OUTPUT_DIR is ${OUTPUT_DIR}")


add_custom_target(release_assimp ALL
        DEPENDS assimp
        COMMAND ${CMAKE_COMMAND} -E make_directory "${OUTPUT_DIR}/$<CONFIG>/binary"
        COMMAND ${CMAKE_COMMAND} -E copy_if_different "$<TARGET_FILE:assimp>" "${OUTPUT_DIR}/$<CONFIG>/binary"
        COMMAND ${CMAKE_COMMAND} -E copy_if_different "$<TARGET_PDB_FILE:assimp>" "${OUTPUT_DIR}/$<CONFIG>/binary"
        COMMAND ${CMAKE_COMMAND} -E make_directory "${OUTPUT_DIR}/$<CONFIG>/library"
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        "$<TARGET_LINKER_FILE:assimp>" "${OUTPUT_DIR}/$<CONFIG>/library"
        COMMENT "Copying assimp to ${OUTPUT_DIR}/$<CONFIG>/binary"
        COMMENT "Copying .pdb file of assimp to $${OUTPUT_DIR}/$<CONFIG>/binary"
        COMMENT "Copying .lib file of assimp to $${OUTPUT_DIR}/$<CONFIG>/library"
)


add_custom_target(release_all)

add_dependencies(release_all release_assimp)



