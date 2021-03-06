cmake_minimum_required(VERSION 2.8)
set(TARGET_NAME superpowered)

# set source files
set(CPP_SOURCE_DIR ${CMAKE_SOURCE_DIR}/src)

if(APPLE)
    set(CPP_FILES ${CPP_SOURCE_DIR}/platform/lua_superpowered_player_common.cpp
            ${CPP_SOURCE_DIR}/platform/SpPlayerBase.cpp
            ${CPP_SOURCE_DIR}/platform/osx/SpPlayer.mm
            ${CPP_SOURCE_DIR}/platform/osx/lua_superpowered_player_osx.cpp
            ${CPP_SOURCE_DIR}/lua_superpowered.cpp
            ${CPP_SOURCE_DIR}/lua_superpowered_player.cpp
            ${CPP_SOURCE_DIR}/Superpowered/SuperpoweredOSXAudioIO.mm
            )
    include_directories(${CPP_SOURCE_DIR}
            ${CPP_SOURCE_DIR}/platform
            ${CPP_SOURCE_DIR}/osx
            ${CPP_SOURCE_DIR}/Superpowered
            )
elseif(CMAKE_SYSTEM_NAME MATCHES "Linux")
    set(CPP_FILES ${CPP_SOURCE_DIR}/platform/linux/lua_superpowered_player_linux.cpp
            ${CPP_SOURCE_DIR}/lua_superpowered.cpp
            ${CPP_SOURCE_DIR}/lua_superpowered_player.cpp
            )
    include_directories(${CPP_SOURCE_DIR}
            ${CPP_SOURCE_DIR}/platform
            ${CPP_SOURCE_DIR}/linux
            ${CPP_SOURCE_DIR}/Superpowered
            )
endif()

add_library(${TARGET_NAME} SHARED ${CPP_FILES})
set_target_properties(${TARGET_NAME} PROPERTIES
    PREFIX ""
    SUFFIX ".so")

# config compile args
set(CMAKE_CXX_FLAGS "-std=c++11 -O3 -Wall -pthread")

if(APPLE)
    message("platform is apple now!")
    # config link args
    find_library(LUA_LIBRARY lua)
    find_library(CORE_FOUNDATION_LIBRARY CoreFoundation)
    find_library(CORE_MEDIA_LIBRARY CoreMedia)
    find_library(CORE_AUDIO_LIBRARY CoreAudio)
    find_library(AVFOUNDATION_LIBRARY AVFoundation)
    find_library(AUDIOTOOLBOX_LIBRARY AudioToolbox)
    find_library(FOUNDATION_LIBRARY Foundation)
    find_library(SUPERPOWERED_LIBRARY SuperpoweredAudioOSX ${CMAKE_SOURCE_DIR}/lib)
    # libraries
    target_link_libraries(${TARGET_NAME}
            ${LUA_LIBRARY}
            ${SUPERPOWERED_LIBRARY}
            ${FOUNDATION_LIBRARY}
            ${CORE_FOUNDATION_LIBRARY}
            ${CORE_MEDIA_LIBRARY}
            ${CORE_AUDIO_LIBRARY}
            ${AVFOUNDATION_LIBRARY}
            ${AUDIOTOOLBOX_LIBRARY}
            )
elseif(CMAKE_SYSTEM_NAME MATCHES "Linux")
    message("platform is linux now!")
    find_library(LUA_LIBRARY lua)
    find_library(SUPERPOWERED_LIBRARY libSuperpoweredLinuxX86_64.a ${CMAKE_SOURCE_DIR}/lib)
    find_library(ALSASOUND_LIBRARY asound)
    target_link_libraries(${TARGET_NAME}
            ${LUA_LIBRARY}
            ${SUPERPOWERED_LIBRARY}
            ${ALSASOUND_LIBRARY}
            )
endif()


# install
set(INSTALL_PATH /usr/local/lib/lua/5.3)
install(TARGETS ${TARGET_NAME} DESTINATION ${INSTALL_PATH} PERMISSIONS OWNER_READ OWNER_WRITE GROUP_READ WORLD_READ)
