cmake_minimum_required(VERSION 3.16)

project(ZaxEditor VERSION 0.2.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

find_package(Qt6 REQUIRED COMPONENTS Core Gui Widgets)

# --- Capstone (x86 disassembler, BSD 3-clause licence) used to read the class /
# property schema out of the user's own Zax.exe at run time. Vendored, trimmed
# copy in third_party/capstone (see ZAXEDITOR_VENDORED.txt); static, x86 only.
include(third_party/capstone.cmake)

# --- Core: .zax model, reader/writer, schema, validator (no GUI) -------------
add_library(zaxcore STATIC
    src/model/ZaxNode.h
    src/model/ZaxNode.cpp
    src/parsers/ZaxParser.h
    src/parsers/ZaxParser.cpp
    src/parsers/ZaxSerializer.h
    src/parsers/ZaxSerializer.cpp
    src/parsers/ZaxBinaryReader.h
    src/parsers/ZaxBinaryReader.cpp
    src/schema/ZaxSchema.h
    src/schema/GameLists.h
    src/schema/GameLists.cpp
    src/schema/ZaxSchema.cpp
    src/schema/ZaxValidator.h
    src/schema/ZaxValidator.cpp
    src/schema/ZaxValueFormat.h
    src/schema/ZaxValueFormat.cpp
    src/schema/ExeSchemaExtractor.h
    src/schema/ExeSchemaExtractor.cpp
    src/schema/PatrolPath.h
    src/schema/PatrolPath.cpp
    src/schema/ActionTargets.h
    src/schema/ActionTargets.cpp
    src/schema/CompanionFiles.h
    src/schema/CompanionFiles.cpp
)
target_include_directories(zaxcore PUBLIC src)
target_link_libraries(zaxcore PUBLIC Qt6::Core PRIVATE capstone)

# --- Graphics: .frm frame decoding (QImage) --------------------------------
add_library(zaxgfx STATIC
    src/parsers/FrmParser.h
    src/parsers/FrmParser.cpp
    src/render/PlasmaRenderer.h
    src/render/PlasmaRenderer.cpp
    src/render/HueRemap.h
    src/render/HueRemap.cpp
    src/render/PolygonRaster.h
    src/render/PolygonRaster.cpp
    src/render/WaterWarp.h
    src/render/WaterWarp.cpp
)
target_include_directories(zaxgfx PUBLIC src)
target_link_libraries(zaxgfx PUBLIC Qt6::Gui)

# --- Editor -------------------------------------------------------------------
set(ZAX_EDITOR_SOURCES
    src/MainWindow.h
    src/MainWindow.cpp
    src/widgets/ProgressDialog.h
    src/widgets/ProgressDialog.cpp
    src/widgets/ProgressDialog.ui
    src/widgets/PropertyValueDelegate.h
    src/widgets/PropertyValueDelegate.cpp
    src/widgets/AssetBrowser.h
    src/widgets/AssetBrowser.cpp
    src/widgets/ConsoleWidget.h
    src/widgets/ConsoleWidget.cpp
    src/utils/EditorLog.h
    src/utils/EditorLog.cpp
    src/edit/EditCommands.h
    src/edit/EditCommands.cpp
    src/edit/ZaxDefaults.h
    src/edit/ZaxDefaults.cpp
    src/edit/NewMapBuilder.h
    src/edit/NewMapBuilder.cpp
    src/edit/TemplateLibrary.h
    src/edit/TemplateLibrary.cpp
    src/edit/InsertObjectDialog.h
    src/edit/InsertObjectDialog.cpp
    src/edit/ItemPickDialog.h
    src/edit/ItemPickDialog.cpp
    src/edit/TemplatePickDialog.h
    src/edit/TemplatePickDialog.cpp
    src/settings/EditorSettings.h
    src/settings/EditorSettings.cpp
    src/utils/FileResolver.h
    src/utils/FileResolver.cpp
    src/view/EntityItem.h
    src/view/EntityItem.cpp
    src/view/PlasmaItem.h
    src/view/PlasmaItem.cpp
    src/view/SpriteStripItem.h
    src/view/SpriteStripItem.cpp
    src/view/PolygonItem.h
    src/view/PolygonItem.cpp
    src/view/MapScene.h
    src/view/MapScene.cpp
    src/view/MapView.h
    src/view/MapView.cpp
    src/view/TextureCache.h
    src/view/TextureCache.cpp
)
add_executable(ZaxEditor src/main.cpp ${ZAX_EDITOR_SOURCES}
    Resources.qrc)
target_link_libraries(ZaxEditor PRIVATE zaxcore zaxgfx Qt6::Widgets)
if(WIN32)
    set_target_properties(ZaxEditor PROPERTIES WIN32_EXECUTABLE TRUE)
endif()

# The self-tests are built only when the tests folder is present (it is not part of
# the published sources).
if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/tests/zax_selftest.cpp")
    find_package(Qt6 REQUIRED COMPONENTS Test)
    # --- Self test: byte-exact round trip + validation of real maps ---------------
    # Usage: zax_selftest <map.zax>...   (reads only; writes nothing)
    add_executable(zax_selftest tests/zax_selftest.cpp)
    target_link_libraries(zax_selftest PRIVATE zaxcore)

    # Usage: frm_selftest <game folder> [png output folder]   (reads only)
    add_executable(frm_selftest tests/frm_selftest.cpp)
    target_link_libraries(frm_selftest PRIVATE zaxgfx)

    # Usage: schema_selftest <game folder> [reference.json] [out.json]   (reads only)
    # Usage: path_selftest <game folder> [missing] [overrun]   (reads only)
    add_executable(path_selftest tests/path_selftest.cpp)
    target_link_libraries(path_selftest PRIVATE zaxcore)
    add_executable(schema_selftest tests/schema_selftest.cpp)
    target_link_libraries(schema_selftest PRIVATE zaxcore)

    # Usage: bin_selftest <game folder> [minimum decoded]   (reads only)
    add_executable(bin_selftest tests/bin_selftest.cpp)
    target_link_libraries(bin_selftest PRIVATE zaxcore)

    # Usage: edit_selftest <map.zax>   (reads only)
    add_executable(edit_selftest tests/edit_selftest.cpp src/edit/EditCommands.cpp src/edit/EditCommands.h
                   src/edit/TemplateLibrary.cpp src/edit/TemplateLibrary.h src/edit/ZaxDefaults.cpp src/edit/ZaxDefaults.h)
    target_link_libraries(edit_selftest PRIVATE zaxcore Qt6::Gui)

    # GUI test with simulated mouse input (run offscreen)
    # Measurements only (not a test): perf_bench <map.zax>
    add_executable(perf_bench tests/perf_bench.cpp ${ZAX_EDITOR_SOURCES})
    target_include_directories(perf_bench PRIVATE src)
    target_link_libraries(perf_bench PRIVATE zaxcore zaxgfx Qt6::Widgets)

    add_executable(gui_selftest tests/gui_selftest.cpp ${ZAX_EDITOR_SOURCES})
    target_include_directories(gui_selftest PRIVATE src)
    target_link_libraries(gui_selftest PRIVATE zaxcore zaxgfx Qt6::Widgets Qt6::Test)

    enable_testing()
    set(ZAX_TEST_MAP_DIR "" CACHE PATH "Folder with .zax maps for the self test (e.g. the game's Levels folder)")
    if(ZAX_TEST_MAP_DIR)
        file(GLOB_RECURSE ZAX_TEST_MAPS "${ZAX_TEST_MAP_DIR}/*.zax" "${ZAX_TEST_MAP_DIR}/*.BAK")
        add_test(NAME zax_roundtrip COMMAND zax_selftest ${ZAX_TEST_MAPS})
        add_test(NAME frm_decode COMMAND frm_selftest "${ZAX_TEST_MAP_DIR}/..")
        add_test(NAME bin_decode COMMAND bin_selftest "${ZAX_TEST_MAP_DIR}/..")
        add_test(NAME gui_mouse COMMAND gui_selftest "${ZAX_TEST_MAP_DIR}/Demo/E3 Hydro Station.zax")
        set_tests_properties(gui_mouse PROPERTIES ENVIRONMENT "QT_QPA_PLATFORM=offscreen")
        set(ZAX_SCHEMA_REFERENCE "${CMAKE_CURRENT_SOURCE_DIR}/../docs/schema/zax_schema_reference.json")
        if(EXISTS "${ZAX_SCHEMA_REFERENCE}")
            add_test(NAME schema_from_exe COMMAND schema_selftest "${ZAX_TEST_MAP_DIR}/.." "${ZAX_SCHEMA_REFERENCE}")
        else()
            add_test(NAME schema_from_exe COMMAND schema_selftest "${ZAX_TEST_MAP_DIR}/..")
        endif()
        add_test(NAME path_rules COMMAND path_selftest "${ZAX_TEST_MAP_DIR}/..")
        add_test(NAME edit_undo COMMAND edit_selftest "${ZAX_TEST_MAP_DIR}/Demo/E3 Hydro Station.zax" "${ZAX_TEST_MAP_DIR}")
    endif()
endif()
