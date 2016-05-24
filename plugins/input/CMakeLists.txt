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

if (PLUGIN_CSV)
    message(STATUS "Adding CSV plugin...")
    add_subdirectory(csv)
endif (PLUGIN_CSV)

if (PLUGIN_GDAL)
    message(STATUS "Adding GDAL plugin...")
    add_subdirectory(gdal)
endif (PLUGIN_GDAL)

if (PLUGIN_GEOJSON)
    message(STATUS "Adding GeoJSON plugin...")
    add_subdirectory(geojson)
endif (PLUGIN_GEOJSON)

if (PLUGIN_OGR)
    message(STATUS "Adding OGR plugin...")
    add_subdirectory(ogr)
endif (PLUGIN_OGR)

if (PLUGIN_RASTER)
    message(STATUS "Adding raster plugin...")
    add_subdirectory(raster)
endif (PLUGIN_RASTER)

if (PLUGIN_SHAPE)
    message(STATUS "Adding shape plugin...")
    add_subdirectory(shape)
endif (PLUGIN_SHAPE)

if (PLUGIN_SQLITE)
    message(STATUS "Adding sqlite plugin...")
    add_subdirectory(sqlite)
endif (PLUGIN_SQLITE)

if (PLUGIN_TOPOJSON)
    message(STATUS "Adding TopoJSON plugin...")
    add_subdirectory(topojson)
endif (PLUGIN_TOPOJSON)

# Need to forward these variables to parent scope.
set(MAPNIK_PLUGINS ${MAPNIK_PLUGINS} PARENT_SCOPE)
set(MAPNIK_BINARY_DIRECTORIES ${MAPNIK_BINARY_DIRECTORIES} PARENT_SCOPE)
