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

include_directories(${CMAKE_CURRENT_SOURCE_DIR})


# Unit tests
file(GLOB unit_test_sources
     LIST_DIRECTORIES false
     unit/*/*.cpp
     unit/*.cpp)
     
add_executable(run_unit_test ${unit_test_sources})

target_link_libraries(run_unit_test mapnik mapnik-json mapnik-wkt)


# Standalone tests
unset(standalone_test_names)

function (add_standalone_test name dependencies)
    set(test_name "run_${name}")
    add_executable(${test_name} "standalone/${name}.cpp")
    target_link_libraries(${test_name} mapnik ${dependencies})
    set(standalone_test_names ${standalone_test_names} ${test_name} PARENT_SCOPE)
endfunction (add_standalone_test)

add_standalone_test("agg_rasterizer_integer_overflow_test" mapnik-json)
add_standalone_test("datasource_registration_test" "")
add_standalone_test("font_registration_test" "")


# Visual tests
set(visual_test_sources
    visual/report.cpp
    visual/runner.cpp
    visual/run.cpp
)

add_executable(run_visual_test ${visual_test_sources})

target_link_libraries(run_visual_test mapnik)

# Install rules
install(TARGETS run_unit_test run_visual_test ${standalone_test_names} RUNTIME DESTINATION "bin/${CMAKE_BUILD_TYPE}")
