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

set(mapnik_plugin_gdal_sources
    gdal_datasource.cpp
    gdal_featureset.cpp)

add_library(gdal MODULE ${mapnik_plugin_gdal_sources})

target_compile_definitions(gdal PUBLIC -DMAPNIK_EXPORTS)

set_target_properties(gdal PROPERTIES SUFFIX ".input")

target_link_libraries(gdal ${GDAL_LIBRARY} ${Boost_LIBRARIES} ${ICU_LIBRARIES} mapnik)

install(TARGETS gdal LIBRARY DESTINATION "bin/${CMAKE_BUILD_TYPE}/mapnik/input")

set(MAPNIK_PLUGINS ${MAPNIK_PLUGINS} ${CMAKE_INSTALL_PREFIX}/bin/${CMAKE_BUILD_TYPE}/mapnik/input/gdal.input PARENT_SCOPE)
set(MAPNIK_BINARY_DIRECTORIES ${MAPNIK_BINARY_DIRECTORIES} ${mapnik_plugin_gdal_binary_directories} ${CMAKE_CURRENT_BINARY_DIR} PARENT_SCOPE)
