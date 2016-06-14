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

add_subdirectory(json)
add_subdirectory(wkt)

set(mapnik_sources
    expression_grammar.cpp
    fs.cpp
    request.cpp
    well_known_srs.cpp
    params.cpp
    image_filter_types.cpp
    image_filter_grammar.cpp
    color.cpp
    conversions.cpp
    image_copy.cpp
    image_compositing.cpp
    image_scaling.cpp
    box2d.cpp
    datasource_cache.cpp
    datasource_cache_static.cpp
    debug.cpp
    geometry_reprojection.cpp
    expression_node.cpp
    expression_string.cpp
    expression.cpp
    transform_expression.cpp
    feature_kv_iterator.cpp
    feature_style_processor.cpp
    feature_type_style.cpp
    dasharray_parser.cpp
    font_engine_freetype.cpp
    font_set.cpp
    function_call.cpp
    gradient.cpp
    parse_path.cpp
    image_reader.cpp
    cairo_io.cpp
    image.cpp
    image_view.cpp
    image_view_any.cpp
    image_any.cpp
    image_options.cpp
    image_util.cpp
    image_util_jpeg.cpp
    image_util_png.cpp
    image_util_tiff.cpp
    image_util_webp.cpp
    layer.cpp
    map.cpp
    load_map.cpp
    palette.cpp
    marker_helpers.cpp
    transform_expression_grammar.cpp
    geometry_envelope.cpp
    plugin.cpp
    rule.cpp
    save_map.cpp
    wkb.cpp
    projection.cpp
    proj_transform.cpp
    scale_denominator.cpp
    simplify.cpp
    parse_transform.cpp
    memory_datasource.cpp
    symbolizer.cpp
    symbolizer_keys.cpp
    symbolizer_enumerations.cpp
    unicode.cpp
    raster_colorizer.cpp
    mapped_memory_cache.cpp
    marker_cache.cpp
    svg/svg_parser.cpp
    svg/svg_path_parser.cpp
    svg/svg_points_parser.cpp
    svg/svg_transform_parser.cpp
    warp.cpp
    css_color_grammar.cpp
    vertex_cache.cpp
    text/font_library.cpp
    text/text_layout.cpp
    text/text_line.cpp
    text/itemizer.cpp
    text/scrptrun.cpp
    text/face.cpp
    text/glyph_positions.cpp
    text/placement_finder.cpp
    text/properties_util.cpp
    text/renderer.cpp
    text/symbolizer_helpers.cpp
    text/text_properties.cpp
    text/font_feature_settings.cpp
    text/formatting/base.cpp
    text/formatting/list.cpp
    text/formatting/text.cpp
    text/formatting/format.cpp
    text/formatting/layout.cpp
    text/formatting/registry.cpp
    text/placements/registry.cpp
    text/placements/base.cpp
    text/placements/dummy.cpp
    text/placements/list.cpp
    text/placements/simple.cpp
    group/group_layout_manager.cpp
    group/group_rule.cpp
    group/group_symbolizer_helper.cpp
    xml_tree.cpp
    config_error.cpp
    color_factory.cpp
    renderer_common.cpp
    renderer_common/render_group_symbolizer.cpp
    renderer_common/render_markers_symbolizer.cpp
    renderer_common/render_pattern.cpp
    renderer_common/render_thunk_extractor.cpp
    math.cpp
    
    # add these to the compile flags no matter what
    # to make it safe to try to compile them from Makefile wrapper
    cairo/process_markers_symbolizer.cpp
    cairo/process_group_symbolizer.cpp
    
    # agg backend
    agg/agg_renderer.cpp
    agg/process_dot_symbolizer.cpp
    agg/process_building_symbolizer.cpp
    agg/process_line_symbolizer.cpp
    agg/process_line_pattern_symbolizer.cpp
    agg/process_text_symbolizer.cpp
    agg/process_point_symbolizer.cpp
    agg/process_polygon_symbolizer.cpp
    agg/process_polygon_pattern_symbolizer.cpp
    agg/process_raster_symbolizer.cpp
    agg/process_shield_symbolizer.cpp
    agg/process_markers_symbolizer.cpp
    agg/process_group_symbolizer.cpp
    agg/process_debug_symbolizer.cpp
    
    # add these to the compile flags no matter what
    # to make it safe to try to compile them from Makefile wrapper
    grid/process_markers_symbolizer.cpp
    grid/process_group_symbolizer.cpp
    
    # windows
    util/utf_conv_win.cpp
)


if (NATIVE_GRID_RENDERER)
    list(APPEND mapnik_sources
        grid/grid.cpp
        grid/grid_renderer.cpp
        grid/process_building_symbolizer.cpp
        grid/process_line_pattern_symbolizer.cpp
        grid/process_line_symbolizer.cpp
        grid/process_point_symbolizer.cpp
        grid/process_polygon_pattern_symbolizer.cpp
        grid/process_polygon_symbolizer.cpp
        grid/process_raster_symbolizer.cpp
        grid/process_shield_symbolizer.cpp
        grid/process_text_symbolizer.cpp
    )
endif (NATIVE_GRID_RENDERER)


unset(mapnik_libraries)

if (BUILD_SHARED_LIBS)
    list(APPEND mapnik_libraries agg)
endif()
list(APPEND mapnik_libraries ${Boost_LIBRARIES})
list(APPEND mapnik_libraries ${ICU_LIBRARIES})
list(APPEND mapnik_libraries ${ICONV_LIBRARY})
list(APPEND mapnik_libraries ${ZLIB_LIBRARIES})
list(APPEND mapnik_libraries ${FREETYPE_LIBRARIES})
list(APPEND mapnik_libraries ${HARFBUZZ_LIBRARIES})
list(APPEND mapnik_libraries ${PROJ_LIBRARY})


if (USE_JPEG AND JPEG_FOUND)
    list(APPEND mapnik_sources
        jpeg_reader.cpp
    )
    list(APPEND mapnik_libraries ${JPEG_LIBRARIES})
endif (USE_JPEG AND JPEG_FOUND)

# PNG
if (USE_PNG AND PNG_FOUND)
    list(APPEND mapnik_sources
        png_reader.cpp
    )
    list(APPEND mapnik_libraries ${PNG_LIBRARIES})
endif (USE_PNG AND PNG_FOUND)

# TIFF
if (USE_TIFF AND TIFF_FOUND)
    list(APPEND mapnik_sources
        tiff_reader.cpp
    )
    list(APPEND mapnik_libraries ${TIFF_LIBRARIES})
endif (USE_TIFF AND TIFF_FOUND)

# SVG
if (USE_SVG)
    list(APPEND mapnik_sources
        svg/output/svg_output_grammars.cpp
        svg/output/svg_renderer.cpp
        svg/output/svg_generator.cpp
        svg/output/svg_output_attributes.cpp
        svg/output/process_symbolizers.cpp
        svg/output/process_line_symbolizer.cpp
        svg/output/process_polygon_symbolizer.cpp
    )
endif (USE_SVG)

# libxml2
if (USE_LIBXML2 AND LIBXML2_FOUND)
    list(APPEND mapnik_sources
        libxml2_loader.cpp
    )
    list(APPEND mapnik_libraries ${LIBXML2_LIBRARIES})
else(USE_LIBXML2 AND LIBXML2_FOUND)
    list(APPEND mapnik_sources
        rapidxml_loader.cpp
    )
endif (USE_LIBXML2 AND LIBXML2_FOUND)

# cairo
if (USE_CAIRO AND CAIRO_FOUND)
    # Cairo dependencies
    list(APPEND mapnik_sources
        cairo/cairo_context.cpp
        cairo/cairo_renderer.cpp
        cairo/cairo_render_vector.cpp
        cairo/process_text_symbolizer.cpp
        cairo/process_line_symbolizer.cpp
        cairo/process_line_pattern_symbolizer.cpp
        cairo/process_polygon_symbolizer.cpp
        cairo/process_polygon_pattern_symbolizer.cpp
        cairo/process_debug_symbolizer.cpp
        cairo/process_point_symbolizer.cpp
        cairo/process_raster_symbolizer.cpp
        cairo/process_building_symbolizer.cpp
    )
    list(APPEND mapnik_libraries ${CAIRO_LIBRARIES} ${FONTCONFIG_LIBRARIES})
endif (USE_CAIRO AND CAIRO_FOUND)

# Create the mapnik library
add_library(mapnik ${mapnik_sources})

if (NOT BUILD_SHARED_LIBS AND NOT WIN32)
    set_target_properties(mapnik PROPERTIES COMPILE_FLAGS "-fPIC")
endif()

set_target_properties(mapnik PROPERTIES DEFINE_SYMBOL MAPNIK_EXPORTS)

target_link_libraries(mapnik ${mapnik_libraries} ${CMAKE_DL_LIBS})

# Create install rules
install(TARGETS mapnik RUNTIME DESTINATION "bin/${CMAKE_BUILD_TYPE}")
install(TARGETS mapnik ARCHIVE DESTINATION "lib/MSVC/${CMAKE_BUILD_TYPE}")
