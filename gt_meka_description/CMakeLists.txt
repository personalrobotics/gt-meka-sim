cmake_minimum_required(VERSION 2.8.3)
project(gt_meka_description)

find_package(catkin REQUIRED COMPONENTS xacro)
catkin_package()

set(BASE_OUTPUT_DIR "${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_SHARE_DESTINATION}")
set(URDF_OUTPUT_DIR "${BASE_OUTPUT_DIR}/robots")

file(MAKE_DIRECTORY "${URDF_OUTPUT_DIR}")

macro(xacro_expand input_path output_path)
    add_custom_command(OUTPUT "${output_path}"
        DEPENDS "${input_path}" ${ARGN}
        COMMAND rosrun xacro xacro.py -o "${output_path}" "${input_path}"
    )
endmacro()

xacro_expand("${PROJECT_SOURCE_DIR}/urdf/curi.urdf.xacro"
    "${URDF_OUTPUT_DIR}/curi.urdf"
    # Also Depends:
    "${PROJECT_SOURCE_DIR}/urdf/A2R4_l_arm_defs.urdf.xacro"
    "${PROJECT_SOURCE_DIR}/urdf/A2R4_r_arm_defs.urdf.xacro"
    "${PROJECT_SOURCE_DIR}/urdf/B1R2B_defs.urdf.xacro"
    "${PROJECT_SOURCE_DIR}/urdf/H2R4_l_hand_defs.urdf.xacro"
    "${PROJECT_SOURCE_DIR}/urdf/H2R4_r_hand_defs.urdf.xacro"
    "${PROJECT_SOURCE_DIR}/urdf/HRL_EE_Left.urdf.xacro"
    "${PROJECT_SOURCE_DIR}/urdf/SIMC_head_defs.urdf.xacro"
    "${PROJECT_SOURCE_DIR}/urdf/Z1R2B_defs.urdf.xacro"
    )
add_custom_target(curi_urdf ALL
    DEPENDS "${URDF_OUTPUT_DIR}/curi.urdf"
    COMMENT "Generating Curi URDF"
    VERBATIM
)

add_custom_command(OUTPUT "${URDF_OUTPUT_DIR}/curi.srdf"
    DEPENDS "${PROJECT_SOURCE_DIR}/urdf/curi.srdf"
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
            "${PROJECT_SOURCE_DIR}/urdf/curi.srdf"
            "${URDF_OUTPUT_DIR}/curi.srdf"

)
add_custom_target(curi_srdf ALL
    DEPENDS "${URDF_OUTPUT_DIR}/curi.srdf"
    COMMENT "Generating Curi SRDF"
    VERBATIM
)

install(DIRECTORY "meshes"
                  "${URDF_OUTPUT_DIR}"
        DESTINATION "${CATKIN_PACKAGE_SHARE_DESTINATION}"
        PATTERN ".git" EXCLUDE
)
