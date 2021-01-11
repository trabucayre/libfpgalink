project(fpgalink)

if (${CMAKE_SYSTEM_NAME} MATCHES "Windows")
  set(LIBFPGALINK_LIB_DIR "bin")
else()
  set(LIBFPGALINK_LIB_DIR "lib")
endif()

# Create a shared library
file(GLOB SOURCES src/*.cpp src/*.c)
add_library(${PROJECT_NAME} SHARED ${SOURCES})

# Ensure clients can find the includes
target_include_directories(${PROJECT_NAME} PUBLIC include)

# Dependencies
set(LIB_DEPENDS common error usbwrap buffer fx2loader)
target_link_libraries(${PROJECT_NAME} PUBLIC ${LIB_DEPENDS})

# What to install
install(TARGETS ${PROJECT_NAME}
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION ${LIBFPGALINK_LIB_DIR}
  ARCHIVE DESTINATION ${LIBFPGALINK_LIB_DIR}
)
install(
  DIRECTORY include/makestuff DESTINATION include
)

########################################################################
# Create Pkg Config File
########################################################################
file(TO_NATIVE_PATH "${CMAKE_INSTALL_PREFIX}"                prefix)
file(TO_NATIVE_PATH "\${prefix}"                             exec_prefix)
file(TO_NATIVE_PATH "\${exec_prefix}/${LIBFPGALINK_LIB_DIR}" libdir)
file(TO_NATIVE_PATH "\${prefix}/include"                     includedir)


configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/libfpgalink.pc.in
  ${CMAKE_CURRENT_BINARY_DIR}/libfpgalink.pc
@ONLY)

install(
  FILES ${CMAKE_CURRENT_BINARY_DIR}/libfpgalink.pc
  DESTINATION ${LIBFPGALINK_LIB_DIR}/pkgconfig
)

# Maybe build tests
if(BUILD_TESTING)
  add_subdirectory(tests-unit)
endif()

add_subdirectory(mkfw)
add_subdirectory(flcli)
