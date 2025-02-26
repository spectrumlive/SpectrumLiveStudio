cmake_minimum_required(VERSION 3.16)
project(SpectrumLiveStudio VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_PREFIX_PATH "$ENV{QTDIR}")

find_package(Qt6 COMPONENTS Widgets REQUIRED)

include(cmake/ui-app.cmake)

# Ensure that SOURCES variable is set
if(NOT SOURCES)
    message(FATAL_ERROR "SOURCES variable is empty! Make sure ui-app.cmake is correctly defining it.")
endif()

add_executable(SpectrumLiveStudio MACOSX_BUNDLE ${SOURCES})  # Ensure sources are included

# Platform-specific configurations
if(APPLE)
    set(ICON_NAME AppIcon.icns)
    set(ICON_PATH cmake/macos/${ICON_NAME})
    set(MACOSX_BUNDLE_ICON_FILE ${ICON_PATH})
    set_source_files_properties(${MACOSX_BUNDLE_ICON_FILE} PROPERTIES MACOSX_PACKAGE_LOCATION "Resources")
    file(COPY ${ICON_PATH} DESTINATION "${CMAKE_BINARY_DIR}/bin/${PROJECT_NAME}.app/Contents/Resources")

    set_target_properties(${PROJECT_NAME} PROPERTIES
        MACOSX_BUNDLE ON
        MACOSX_BUNDLE_BUNDLE_NAME ${PROJECT_NAME}
        MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
        MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION}
        MACOSX_BUNDLE_LONG_VERSION_STRING ${PROJECT_VERSION}
        MACOSX_BUNDLE_INFO_PLIST ${CMAKE_CURRENT_SOURCE_DIR}/Info.plist
        MACOSX_BUNDLE_ICON_FILE ${ICON_NAME}
        RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin
        OUTPUT_NAME ${PROJECT_NAME}
    )

    message(STATUS "====> ${CMAKE_BINARY_DIR}/${PROJECT_NAME}.app/Contents/MacOS")

    option(DEPLOYMENT_BUILD "Enable deployment (code signing + DMG packaging)" OFF)

    if(DEPLOYMENT_BUILD)
        message(STATUS "Deployment build enabled: Code signing and DMG packaging")
#
        set(CERTIFICATE_NAME "Apple Development: Nanusorn Photpipat (SCP354B93Y)")
        message(STATUS "CERTIFICATE_NAME: ${CERTIFICATE_NAME}")

        # macdeployqt integration
        add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
            COMMAND rm -f "${CMAKE_BINARY_DIR}/bin/${PROJECT_NAME}.dmg"
            COMMAND /Users/mcduck/Qt/6.5.3/macos/bin/macdeployqt
            ${CMAKE_BINARY_DIR}/bin/${PROJECT_NAME}.app -dmg
        )

        # Code signing (replace with your Apple Developer ID)
        add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
            COMMAND codesign --deep --force --verbose
            --sign ${CERTIFICATE_NAME} ${CMAKE_BINARY_DIR}/bin/${PROJECT_NAME}.app
            VERBATIM
        )

        # Create DMG
        add_custom_target(create_dmg ALL
            COMMAND hdiutil create -volname ${PROJECT_NAME}
            -srcfolder ${CMAKE_BINARY_DIR}/bin/${PROJECT_NAME}.app -ov -format UDZO ${CMAKE_BINARY_DIR}/bin/${PROJECT_NAME}.dmg
            DEPENDS ${PROJECT_NAME}
            VERBATIM
        )
    else()
        message(STATUS "Development build: No code signing or DMG packaging")
    endif()

elseif(WIN32)
    set(WINDOWS_ICON AppIcon.rc)
    add_executable(${PROJECT_NAME} WIN32 ${SOURCES} ${WINDOWS_ICON})

    # Windows deployment using windeployqt
    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
            COMMAND windeployqt --release $<TARGET_FILE:${PROJECT_NAME}>
    )

elseif(UNIX)
    add_executable(${PROJECT_NAME} ${SOURCES})

    # Install Linux desktop file and icon
    install(TARGETS ${PROJECT_NAME} DESTINATION bin)
    install(FILES AppIcon.png DESTINATION share/icons/hicolor/128x128/apps)
    install(FILES ${PROJECT_NAME}.desktop DESTINATION share/applications)
endif()

target_link_libraries(${PROJECT_NAME} PRIVATE Qt6::Widgets)