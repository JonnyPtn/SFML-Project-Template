cmake_minimum_required(VERSION 2.8)

# Project name
project(MyProject)

# Add our modules folder
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake/modules")

# Set the SFML Version
SET(SFML_MIN_VERSION 2.4)

# Find SFML libs and includes
find_package(SFML ${SFML_MIN_VERSION} REQUIRED graphics audio window system network)

# GLOB search for the project files
file(GLOB INCLUDE "include/*.hpp")
file(GLOB SOURCE "src/*.cpp")

# Now we add the project executable, if we're on Apple we'll create an app bundle
if(APPLE)
    set(MACOSX_BUNDLE_GUI_IDENTIFIER ${PROJECT_NAME})
    set(MACOSX_BUNDLE_BUNDLE_NAME ${PROJECT_NAME})
    add_executable(${PROJECT_NAME} MACOSX_BUNDLE ${INCLUDE} ${SOURCE})
else()
    add_executable(${PROJECT_NAME} ${INCLUDE} ${SOURCE})
endif()

# Set the project include directories
target_include_directories(${SFML_INCLUDE_DIRS})

# Add the SFML libraries and dependecies for the linker
target_link_libraries(${PROJECT_NAME} ${SFML_LIBRARIES} ${SFML_DEPENDENCIES})

# Create an install target
install(TARGETS ${PROJECT_NAME} 
    BUNDLE DESTINATION .
	RUNTIME DESTINATION .)

# Create a package target
# On windows, this creates an NSIS installer
# On Linux, it creates a deb package
# On osx, it creates a drag 'n' drop installer
if(APPLE)
	set(CPACK_PACKAGE_FILE_NAME)
    install(CODE " include(BundleUtilities)
		 fixup_bundle(${CMAKE_INSTALL_PREFIX}/${PROJECT_NAME}.app \"\" \"/Library/Frameworks\")
		 verify_app(${CMAKE_INSTALL_PREFIX}/${PROJECT_NAME}.app)")
    SET(CPACK_GENERATOR DragNDrop)
elseif(UNIX)
	set(CPACK_GENERATOR DEB)
	set(CPACK_PACKAGE_CONTACT someone@email.com)
elseif(WIN32)
	set(CPACK_GENERATOR NSIS)
endif()


# Finally include cpack (this must be done after setting package variables)
include(CPack)
