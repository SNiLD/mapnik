# This file is addtitional part of Mapnik (c++ mapping toolkit)
#
# Copyright (C) 2016 Toni Heimala
#
# Mapnik is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License, or (at your option) any later version.
#
# This library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# Lesser General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public
# License along with this library; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA

cmake_minimum_required (VERSION 2.8)

project (mapnik)

include(FeatureSummary)

set(CMAKE_INSTALL_PREFIX "${CMAKE_CURRENT_BINARY_DIR}/../Deployment/${CMAKE_BUILD_TYPE}" CACHE INTERNAL "Default installation path")

option(USE_JPEG "Compile with JPEG support" TRUE)
option(USE_PNG "Compile with PNG support" TRUE)
option(USE_TIFF "Compile with TIFF support" TRUE)
option(USE_SVG "Compile with SVG support" TRUE)
option(USE_LIBXML2 "Use libxml2 as XML library" TRUE)
option(USE_CAIRO "Use cairo as rendering library" TRUE)
option(PLUGIN_CSV "Compile CSV plugin" TRUE)
option(PLUGIN_GDAL "Compile GDAL plugin" TRUE)
option(PLUGIN_GEOJSON "Compile GeoJSON plugin" TRUE)
option(PLUGIN_OGR "Compile OGR plugin" TRUE)
option(PLUGIN_RASTER "Compile raster plugin" TRUE)
option(PLUGIN_SHAPE "Compile shape plugin" TRUE)
option(PLUGIN_SQLITE "Compile sqlite plugin" TRUE)
option(PLUGIN_TOPOJSON "Compile TopoJSON plugin" TRUE)
option(MAPNIK_MEMORY_MAPPED_FILE "Utilize memory-mapped files in plugins (higher memory usage, better performance)" TRUE)
option(NATIVE_GRID_RENDERER "Build support for native grid renderer" TRUE)
option(ENABLE_STATS "If set to TRUE then timing output will be printed to stderr of a variety of performance critical code paths. The default value is FALSE." FALSE)
option(ENABLE_LOG "This option controls which severity levels should be compiled into the Mapnik binary. The default value is FALSE. This default means that not all severity types will be compiled into Mapnik: only error and none levels will be available to toggle at runtime. The reason for this is to ensure that Mapnik runs fast by default, because verbose logging can slow down code execution. If set to TRUE then logging of all severity types will be enabled and available at runtime. NOTE: This option defaults to TRUE if in debug builds." FALSE)
set(DEFAULT_LOG_SEVERITY "error" CACHE STRING "The DEFAULT_LOG_SEVERITY option controls which severity level will be used by default at runtime - the value it will be initialized to at startup. It is not recommended to change this option at configuration time, unless you are a developer or doing customized packaging of Mapnik. Users can change the severity level easily at runtime. The default level is error. Other named severity levels are: debug, warn, none. An important caveat is that the ENABLE_LOG option controls which severity levels are compiled into Mapnik by default, so setting DEFAULT_LOG_SEVERITY to warn or debug will have no effect unless ENABLE_LOG is TRUE.")
option(RENDERING_STATS "This is an experimental option that may be removed in future releases. It defaults to FALSE, but can be set to TRUE to enable verbose logging of rendering behavior providing the timing of style and layer processing." FALSE)
option(THREAD_SAFE "Build mapnik in thread-safe mode" TRUE)

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake/Modules)

include_directories(
    ${mapnik_SOURCE_DIR}
    ${mapnik_BINARY_DIR}
    ${mapnik_SOURCE_DIR}/deps
    ${mapnik_SOURCE_DIR}/deps/agg/include
    ${mapnik_SOURCE_DIR}/deps/clipper/include
    ${mapnik_SOURCE_DIR}/include
)

# Mapnik 3.x depends on c++11 standard.
set (CMAKE_CXX_STANDARD 11)

set(MAPNIK_PLUGINS "" CACHE INTERNAL "Full paths to mapnik plugins. Automatically filled.")
set(MAPNIK_BINARY_DIRECTORIES "" CACHE INTERNAL "Full paths to directories of dependencies. Automatically filled.")

# Enable logging for debug builds.
if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(ENABLE_LOG TRUE)
endif (CMAKE_BUILD_TYPE STREQUAL "Debug")

add_feature_info("JPEG support" USE_JPEG "Compile with JPEG support")
add_feature_info("PNG support" USE_PNG "Compile with PNG support")
add_feature_info("TIFF support" USE_TIFF "Compile with TIFF support")
add_feature_info("SVG support" USE_SVG "Compile with SVG support")
add_feature_info("libxml2 library" USE_LIBXML2 "Use libxml2 library")
add_feature_info("cairo library" USE_CAIRO "Use cairo renderer library")

add_feature_info("CSV plugin" PLUGIN_CSV "CSV plugin")
add_feature_info("GDAL plugin" PLUGIN_GDAL "GDAL plugin")
add_feature_info("GeoJSON plugin" PLUGIN_GEOJSON "GeoJSON plugin")
add_feature_info("OGR plugin" PLUGIN_OGR "OGR plugin")
add_feature_info("raster plugin" PLUGIN_RASTER "raster plugin")
add_feature_info("shape plugin" PLUGIN_SHAPE "shape plugin")
add_feature_info("sqlite plugin" PLUGIN_SQLITE "sqlite plugin")
add_feature_info("TopoJSON plugin" PLUGIN_TOPOJSON "TopoJSON plugin")

add_feature_info("Thread safe" THREAD_SAFE "Compile to use thread locks")
add_feature_info("Statistics" ENABLE_STATS "Compile with extensive statistics")
add_feature_info("Logging" ENABLE_LOG "Compile with logging support")
add_feature_info("Rendering statistics" RENDERING_STATS "Compile with rendering stats")

if (THREAD_SAFE)
    add_definitions(-DMAPNIK_THREADSAFE)
endif (THREAD_SAFE)

if (ENABLE_STATS)
    add_definitions(-DMAPNIK_STATS)
endif (ENABLE_STATS)

if (ENABLE_LOG)
    add_definitions(-DMAPNIK_LOG -DMAPNIK_DEFAULT_LOG_SEVERITY=${DEFAULT_LOG_SEVERITY})
endif (ENABLE_LOG)

if (RENDERING_STATS)
    add_definitions(-DRENDERING_STATS)
endif (RENDERING_STATS)

if (MAPNIK_MEMORY_MAPPED_FILE)
    add_definitions(-DMAPNIK_MEMORY_MAPPED_FILE)
endif (MAPNIK_MEMORY_MAPPED_FILE)


# Find required dependencies in advance


# boost
set(Boost_USE_STATIC_LIBS OFF)
set(Boost_USE_STATIC_RUNTIME OFF)
find_package(Boost REQUIRED COMPONENTS filesystem regex system)
set_package_properties(Boost PROPERTIES
    DESCRIPTION "Portable C++ source libraries"
    URL "http://www.boost.org/"
    TYPE REQUIRED
    PURPOSE "Required for all core operations.")
include_directories(${Boost_INCLUDE_DIRS})
add_definitions(-DBoost_LIB_DIAGNOSTIC_DEFINITIONS -DBOOST_ALL_DYN_LINK -DBOOST_ALL_NO_LIB)
get_filename_component(boost_binary_directories ${Boost_LIBRARY_DIRS}/../bin ABSOLUTE)
list(APPEND MAPNIK_BINARY_DIRECTORIES ${boost_binary_directories})


# ICU
set(icu_components uc)

# Check whether the boost libary uses ICU.
include(CheckCXXSourceRuns)

find_package(ICU REQUIRED COMPONENTS uc i18n)

set(CMAKE_REQUIRED_LIBRARIES ${ICU_LIBRARIES})

if (CMAKE_BUILD_TYPE EQUAL "Debug")
    list(APPEND CMAKE_REQUIRED_LIBRARIES ${Boost_REGEX_LIBRARY_DEBUG})
else (CMAKE_BUILD_TYPE EQUAL "Debug")
    list(APPEND CMAKE_REQUIRED_LIBRARIES ${Boost_REGEX_LIBRARY_RELEASE})
endif (CMAKE_BUILD_TYPE EQUAL "Debug")

set(CMAKE_REQUIRED_DEFINITIONS -DBOOST_ALL_DYN_LINK -DBOOST_ALL_NO_LIB)
set(CMAKE_REQUIRED_INCLUDES ${Boost_INCLUDE_DIRS} ${ICU_INCLUDE_DIRS})
file(READ ${CMAKE_MODULE_PATH}/CheckBoostRegexIcu.cpp CheckBoostRegexIcuSource)
CHECK_CXX_SOURCE_RUNS("${CheckBoostRegexIcuSource}" BOOST_REGEX_HAS_ICU)

if (BOOST_REGEX_HAS_ICU)
    list(APPEND icu_components i18n)
endif (BOOST_REGEX_HAS_ICU)

find_package(ICU REQUIRED COMPONENTS ${icu_components})
set_package_properties(ICU PROPERTIES
    DESCRIPTION "Unicode and Globalization support library"
    URL "http://site.icu-project.org/"
    TYPE REQUIRED
    PURPOSE "Required for text conversions.")
include_directories(${ICU_INCLUDE_DIRS})
get_filename_component(icu_binary_directories ${ICU_INCLUDE_DIRS}/../bin ABSOLUTE)
list(APPEND MAPNIK_BINARY_DIRECTORIES ${icu_binary_directories})


# iconv
find_package(ICONV REQUIRED)
set_package_properties(ICONV PROPERTIES
    DESCRIPTION "String conversion library"
    URL "http://www.gnu.org/software/libiconv/"
    TYPE REQUIRED
    PURPOSE "Required for text conversions (also dependency for some libraries).")
include_directories(${ICONV_INCLUDE_DIR})
get_filename_component(iconv_binary_directories ${ICONV_INCLUDE_DIR}/../bin ABSOLUTE)
list(APPEND MAPNIK_BINARY_DIRECTORIES ${iconv_binary_directories})


# zlib
find_package(ZLIB REQUIRED)
set_package_properties(ZLIB PROPERTIES
    DESCRIPTION "Compression library"
    URL "http://www.zlib.net/"
    TYPE REQUIRED
    PURPOSE "Required for processing compressed material (also dependency for some libraries).")
include_directories(${ZLIB_INCLUDE_DIRS})
get_filename_component(zlib_binary_directories ${ZLIB_INCLUDE_DIRS}/../bin ABSOLUTE)
list(APPEND MAPNIK_BINARY_DIRECTORIES ${zlib_binary_directories})


# freetype
find_package(Freetype REQUIRED)
set_package_properties(Freetype PROPERTIES
    DESCRIPTION "Font rendering library"
    URL "http://www.freetype.org/"
    TYPE REQUIRED
    PURPOSE "Required for all font rendering.")
include_directories(${FREETYPE_INCLUDE_DIRS})
get_filename_component(freetype_binary_directories ${FREETYPE_LIBRARY}/../../bin ABSOLUTE)
list(APPEND MAPNIK_BINARY_DIRECTORIES ${freetype_binary_directories})


# harfbuzz
find_package(HarfBuzz REQUIRED)
set_package_properties(HarfBuzz PROPERTIES
    DESCRIPTION "OpenType text shaping engine"
    URL "http://harfbuzz.org/"
    TYPE REQUIRED
    PURPOSE "Required for all font rendering.")
include_directories(${HARFBUZZ_INCLUDE_DIRS})
get_filename_component(harfbuzz_binary_directories ${HARFBUZZ_LIBRARY}/../../bin ABSOLUTE)
list(APPEND MAPNIK_BINARY_DIRECTORIES ${harfbuzz_binary_directories})


# proj.4
find_package(Proj REQUIRED)
set_package_properties(Proj PROPERTIES
    DESCRIPTION "Cartographic Projections library"
    URL "https://trac.osgeo.org/proj/"
    TYPE REQUIRED
    PURPOSE "Required for all projection transformations.")
include_directories(${PROJ_INCLUDE_DIR})
add_definitions(-DMAPNIK_USE_PROJ4)
get_filename_component(proj_binary_directories ${PROJ_INCLUDE_DIR}/../bin ABSOLUTE)
list(APPEND MAPNIK_BINARY_DIRECTORIES ${proj_binary_directories})


if (PLUGIN_GDAL OR PLUGIN_OGR)
    find_package(GDAL REQUIRED)
    set_package_properties(GDAL PROPERTIES
        DESCRIPTION "Translator library for raster geospatial data formats"
        URL "http://www.gdal.org/"
        TYPE REQUIRED
        PURPOSE "Enables reading/writing several vector and raster formats.")
    
    include_directories(${GDAL_INCLUDE_DIR})
    get_filename_component(gdal_binary_directories ${GDAL_INCLUDE_DIR}/../bin ABSOLUTE)
    list(APPEND MAPNIK_BINARY_DIRECTORIES ${gdal_binary_directories})
endif()


if (PLUGIN_SQLITE)
    find_package(Sqlite3 REQUIRED)
    set_package_properties(Sqlite3 PROPERTIES
        DESCRIPTION "Software library that implements a self-contained, serverless, zero-configuration, transactional SQL database engine."
        URL "http://www.sqlite.org/"
        TYPE REQUIRED
        PURPOSE "Enables queries from SQLite databases.")
    
    include_directories(${SQLITE3_INCLUDE_DIR})
    get_filename_component(sqlite3_binary_directories ${SQLITE3_LIBRARY}/../../bin ABSOLUTE)
    list(APPEND MAPNIK_BINARY_DIRECTORIES ${sqlite3_binary_directories})
    
    find_package(SPATIALITE)
    set_package_properties(SPATIALITE PROPERTIES
        DESCRIPTION "Library extending the basic SQLite core in order to get a full fledged Spatial DBMS, really simple and lightweight, but mostly OGC-SFS compliant."
        URL "http://www.gaia-gis.it/gaia-sins/"
        TYPE RECOMMENDED
        PURPOSE "Enables spatial queries from SQLite databases.")
    
    if (SPATIALITE_FOUND)
        include_directories(${SPATIALITE_INCLUDE_DIR})
        get_filename_component(spatialite_binary_directories ${SPATIALITE_LIBRARY}/../../bin ABSOLUTE)
        list(APPEND MAPNIK_BINARY_DIRECTORIES ${spatialite_binary_directories})
    endif (SPATIALITE_FOUND)
endif()


# Additional includes from deps

include_directories(deps/agg/include)
include_directories(deps/mapbox/variant/include)


add_subdirectory(deps)
add_subdirectory(src)
add_subdirectory(plugins)


# Fetch dependencies on install with BundleUtilities
list(REMOVE_DUPLICATES MAPNIK_BINARY_DIRECTORIES)
install(CODE "
    unset(mapnik_plugins_absolute)
    foreach(mapnik_plugin \"${MAPNIK_PLUGINS}\")
        get_filename_component(mapnik_plugin_absolute \"\${mapnik_plugin}\" ABSOLUTE)
        list(APPEND mapnik_plugins_absolute \"\${mapnik_plugin_absolute}\")
    endforeach(mapnik_plugin)
    
    unset(mapnik_binary_directories_absolute)
    foreach(mapnik_binary_directory \"${MAPNIK_BINARY_DIRECTORIES}\")
        get_filename_component(mapnik_binary_directory_absolute \"\${mapnik_binary_directory}\" ABSOLUTE)
        list(APPEND mapnik_binary_directories_absolute \"\${mapnik_binary_directory_absolute}\")
    endforeach(mapnik_binary_directory)
    
    get_filename_component(mapnik_binary_absolute \"${CMAKE_INSTALL_PREFIX}/bin/${CMAKE_BUILD_TYPE}/mapnik${CMAKE_EXECUTABLE_SUFFIX}\" ABSOLUTE)
    include(BundleUtilities)
    fixup_bundle(
        \"\${mapnik_binary_absolute}\"
        \"\${mapnik_plugins_absolute}\"
        \"\${mapnik_binary_directories_absolute}\")
    if(WIN32)
        file(REMOVE \${mapnik_binary_absolute})
    endif(WIN32)
")


# Install includes
install(DIRECTORY include DESTINATION . FILES_MATCHING PATTERN "*.hpp")

feature_summary(WHAT ALL VAR enabledFeaturesText)

message(STATUS "${enabledFeaturesText}")
