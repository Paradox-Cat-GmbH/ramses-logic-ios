#  -------------------------------------------------------------------------
#  Copyright (C) 2021 BMW AG
#  -------------------------------------------------------------------------
#  This Source Code Form is subject to the terms of the Mozilla Public
#  License, v. 2.0. If a copy of the MPL was not distributed with this
#  file, You can obtain one at https://mozilla.org/MPL/2.0/.
#  -------------------------------------------------------------------------

add_library(ramses-logic-viewer-obj OBJECT)

if(ramses-logic_PLATFORM)
    target_sources(ramses-logic-viewer-obj PRIVATE
        ImguiClientHelper.h
        ImguiClientHelper.cpp
        LogicViewerGuiApp.h
        LogicViewerGuiApp.cpp
        LogicViewerGui.h
        LogicViewerGui.cpp
        SceneSetup.h
        )
endif()

target_sources(ramses-logic-viewer-obj PRIVATE
    LogicViewerHeadlessApp.h
    LogicViewerHeadlessApp.cpp
    LogicViewerApp.h
    LogicViewerApp.cpp
    LogicViewer.h
    LogicViewer.cpp
    LogicViewerLog.h
    LogicViewerLog.cpp
    LogicViewerSettings.h
    LogicViewerSettings.cpp
    LogicViewerLuaTypes.h
    LogicViewerLuaTypes.cpp
    Arguments.h
    UpdateReportSummary.h
    )

target_include_directories(ramses-logic-viewer-obj
    PUBLIC
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/lib>
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/tools/ramses-logic-utils>
    $<BUILD_INTERFACE:${PROJECT_BINARY_DIR}/BuildConfig>
)

target_link_libraries(ramses-logic-viewer-obj PUBLIC rlogic::ramses-logic-utils imgui fmt::fmt sol2::sol2 lua::lua CLI11::CLI11)

if(TARGET rlogic::ramses-logic)
    target_link_libraries(ramses-logic-viewer-obj PUBLIC rlogic::ramses-logic)
else()
    target_link_libraries(ramses-logic-viewer-obj PUBLIC rlogic::ramses-logic-static)
endif()

if(ramses-logic_PLATFORM)
    add_executable(ramses-logic-viewer main.cpp)
    target_link_libraries(ramses-logic-viewer PUBLIC ramses-logic-viewer-obj)
    if (ramses-logic_ENABLE_INSTALL)
        install(
            TARGETS ramses-logic-viewer
            DESTINATION ${RLOGIC_INSTALL_RUNTIME_PATH})
    endif()
    folderize_target(ramses-logic-viewer "tools")
else()
    message(STATUS "ramses-logic-viewer is not built (requires RAMSES with renderer support)")
endif()

add_executable(ramses-logic-viewer-headless main_headless.cpp)
target_link_libraries(ramses-logic-viewer-headless PUBLIC ramses-logic-viewer-obj)

folderize_target(ramses-logic-viewer-headless "tools")
folderize_target(ramses-logic-viewer-obj "tools")

if (ramses-logic_ENABLE_INSTALL)
    install(
        TARGETS ramses-logic-viewer-headless
        DESTINATION ${RLOGIC_INSTALL_RUNTIME_PATH})
endif()

if(ramses-logic_BUILD_TESTS)
    add_subdirectory(test)
endif()
