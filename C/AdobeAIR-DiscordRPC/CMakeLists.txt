cmake_minimum_required(VERSION 3.20)

project(AdobeAIRDiscordRPC LANGUAGES C)

set(DISCORD_RPC_LIBRARY "" CACHE FILEPATH "Path to the discord-rpc static library")
set(FRE_LIBRARY "${CMAKE_CURRENT_SOURCE_DIR}/FlashRuntimeExtensions.lib" CACHE FILEPATH "Path to the AIR FlashRuntimeExtensions import library")

if(NOT DISCORD_RPC_LIBRARY)
    message(FATAL_ERROR "DISCORD_RPC_LIBRARY must point to the built discord-rpc library")
endif()

add_library(AdobeAIR-DiscordRPC SHARED
    dllmain.c
)

target_include_directories(AdobeAIR-DiscordRPC PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
)

target_link_libraries(AdobeAIR-DiscordRPC PRIVATE
    ${DISCORD_RPC_LIBRARY}
)

if(WIN32)
    if(NOT EXISTS "${FRE_LIBRARY}")
        message(FATAL_ERROR "FRE_LIBRARY must point to FlashRuntimeExtensions.lib on Windows")
    endif()

    target_link_libraries(AdobeAIR-DiscordRPC PRIVATE
        ${FRE_LIBRARY}
        psapi
        advapi32
    )
endif()

set_target_properties(AdobeAIR-DiscordRPC PROPERTIES
    OUTPUT_NAME "AdobeAIR-DiscordRPC"
    PREFIX ""
    C_STANDARD 99
    C_STANDARD_REQUIRED YES
)

if(APPLE)
    target_link_libraries(AdobeAIR-DiscordRPC PRIVATE
        pthread
        "-framework AppKit"
    )

    target_link_options(AdobeAIR-DiscordRPC PRIVATE
        "-undefined"
        "dynamic_lookup"
    )

    set_target_properties(AdobeAIR-DiscordRPC PROPERTIES
        FRAMEWORK TRUE
    )
endif()
