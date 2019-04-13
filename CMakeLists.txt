CMAKE_MINIMUM_REQUIRED(VERSION 3.8)


PROJECT(SDL2_image VERSION 2.0.4)

set(SDL_IMG "SDL2_image")

set(SDL_IMG_VERSION_MAJOR 2)
set(SDL_IMG_VERSION_MINOR 0)
set(SDL_IMG_VERSION_PATCH 4)

set(SDL_IMG_VERSION "${SDL_IMG_VERSION_MAJOR}.${SDL_IMG_VERSION_MINOR}.${SDL_IMG_VERSION_PATCH}")

set(ENABLE_DEFS "")

set(CONFIG_DEBUG_POSTFIX "d" CACHE STRING "")

#option(FEATURE_ENABLE_BMP "ENABLE_BMP" ON)
#option(FEATURE_ENABLE_GIF "ENABLE_GIF" ON)
#option(FEATURE_ENABLE_JPG "ENABLE_JPG" ON)
#option(FEATURE_ENABLE_LBM "ENABLE_LBM" ON)
#option(FEATURE_ENABLE_PCX "ENABLE_PCX" ON)
option(FEATURE_ENABLE_PNG "ENABLE_PNG" ON)
#option(FEATURE_ENABLE_PNM "ENABLE_PNM" ON)
#option(FEATURE_ENABLE_TGA "ENABLE_TGA" ON)
#option(FEATURE_ENABLE_TIF "ENABLE_TIF" ON)
#option(FEATURE_ENABLE_WEBP "ENABLE_WEBP" OFF) # BROKEN
#option(FEATURE_ENABLE_XCF "ENABLE_XCF" ON)
#option(FEATURE_ENABLE_XPM "ENABLE_XPM" ON)
#option(FEATURE_ENABLE_XV "ENABLE_XV" ON)

add_library(${SDL_IMG})

find_package(SDL2 REQUIRED)
target_link_libraries(${SDL_IMG} PRIVATE SDL2::SDL2)

set_property(TARGET ${SDL_IMG} PROPERTY DEBUG_POSTFIX ${CONFIG_DEBUG_POSTFIX})

# if(FEATURE_ENABLE_BMP)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_BMP)
# endif()

# if(FEATURE_ENABLE_GIF)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_GIF)
# endif()

# if(FEATURE_ENABLE_JPG)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_JPG)
    # find_package(JPEG REQUIRED)
    # set(LINK_LIBS "${LINK_LIBS}" "${JPEG_LIBRARIES}")
    # set(INCLUDE_DIRS "${INCLUDE_DIRS}" "${JPEG_INCLUDE_DIRS}")
# endif()

# if(FEATURE_ENABLE_LBM)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_LBM)
# endif()

# if(FEATURE_ENABLE_PCX)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_PCX)
# endif()

if(FEATURE_ENABLE_PNG)
    list(APPEND ENABLE_DEFS LOAD_PNG)

    find_package(PNG REQUIRED NAMES libpng16)
    target_link_libraries(${SDL_IMG} PRIVATE PNG::png)
endif()

# if(FEATURE_ENABLE_PNM)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_PNM)
# endif()

# if(FEATURE_ENABLE_TGA)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_TGA)
# endif()

# if(FEATURE_ENABLE_TIF)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_TIF)
    # find_package(TIFF REQUIRED)
    # set(LINK_LIBS "${LINK_LIBS}" "${TIFF_LIBRARIES}")
    # set(INCLUDE_DIRS "${INCLUDE_DIRS}" "${TIFF_INCLUDE_DIRS}")
# endif()

# if(FEATURE_ENABLE_WEBP)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_WEBP)
    # find_package(WebP REQUIRED) # BROKEN, debian doesn't install the cmake config files, so find_package will fail
    # set(LINK_LIBS "${LINK_LIBS}" "${WebP_LIBRARIES}")
    # set(INCLUDE_DIRS "${INCLUDE_DIRS}" "${WebP_INCLUDE_DIRS}")
# endif()

# if(FEATURE_ENABLE_XCF)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_XCF)
# endif()

# if(FEATURE_ENABLE_XV)
    # set(ENABLE_DEFS "${ENABLE_DEFS}" LOAD_XV)
# endif()

target_sources(
    ${SDL_IMG}
    PRIVATE
        IMG.c
        IMG_bmp.c
        IMG_gif.c
        IMG_jpg.c
        IMG_lbm.c
        IMG_pcx.c
        IMG_png.c
        IMG_pnm.c
        IMG_svg.c
        IMG_tga.c
        IMG_tif.c
        IMG_webp.c
        IMG_xcf.c
        IMG_xpm.c
        IMG_xv.c
        IMG_xxx.c
)

target_include_directories(
    ${SDL_IMG}
    PRIVATE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    PUBLIC $<INSTALL_INTERFACE:"include/SDL2">
)

target_compile_definitions(${SDL_IMG} PRIVATE "${ENABLE_DEFS}")

set(SDL_IMG_PUBLIC_HEADERS "SDL_image.h")
set_target_properties(${SDL_IMG} PROPERTIES PUBLIC_HEADER "${SDL_IMG_PUBLIC_HEADERS}")

install(
    TARGETS ${SDL_IMG}
    EXPORT ${SDL_IMG}-exports
    ARCHIVE DESTINATION "lib${LIB_SUFFIX}"
    LIBRARY DESTINATION "lib${LIB_SUFFIX}"
    RUNTIME DESTINATION "bin"
    PUBLIC_HEADER DESTINATION "include/SDL2"
)

if(APPLE)
    set(PKG_PREFIX "SDL2_image.framework/Resources")
elseif(WIN32)
    set(PKG_PREFIX "cmake")
else()
    set(PKG_PREFIX "lib/cmake/SDL2_image")
endif()

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "${CMAKE_BINARY_DIR}/SDL2_image-config-version.cmake"
    VERSION "${SDL_IMG_VERSION}"
    COMPATIBILITY AnyNewerVersion
)

install(
    EXPORT ${SDL_IMG}-exports
    FILE ${SDL_IMG}-targets.cmake
    NAMESPACE SDL2::
    DESTINATION "${PKG_PREFIX}"
)

install(
    FILES
        "${CMAKE_CURRENT_SOURCE_DIR}/SDL2_image-config.cmake"
        "${CMAKE_BINARY_DIR}/SDL2_image-config-version.cmake"
    DESTINATION "${PKG_PREFIX}"
)
