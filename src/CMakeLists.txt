set(LUA_CORE_SRC
        lapi.c
        lcode.c
        lctype.c
        ldebug.c
        ldo.c
        ldump.c
        lfunc.c
        lgc.c
        llex.c
        lmem.c
        lobject.c
        lopcodes.c
        lparser.c
        lstate.c
        lstring.c
        ltable.c
        ltm.c
        lundump.c
        lvm.c
        lzio.c
        )

set(LUA_LIB_SRC
        lauxlib.c
        lbaselib.c
        lcorolib.c
        ldblib.c
        liolib.c
        lmathlib.c
        loadlib.c
        loslib.c
        lstrlib.c
        ltablib.c
        lutf8lib.c
        linit.c
        )

include(CheckIncludeFile)
CHECK_INCLUDE_FILE("readline/readline.h" HAVE_READLINE_READLINE_H)

add_library(lua_static_lib ${LUA_CORE_SRC} ${LUA_LIB_SRC})
target_compile_definitions(lua_static_lib PRIVATE "LUA_COMPAT_5_3")
target_link_libraries(lua_static_lib m)

if(UNIX)
    target_link_libraries(lua_static_lib dl)
endif()

include(CheckIncludeFile)
CHECK_INCLUDE_FILE("readline/readline.h" HAVE_READLINE_READLINE_H)

add_executable(lua lua.c)
target_link_libraries(lua PRIVATE lua_static_lib)

add_executable(luac luac.c)
target_link_libraries(luac PRIVATE lua_static_lib)

if (HAVE_READLINE_READLINE_H)
    target_compile_definitions(lua PRIVATE "LUA_USE_READLINE")
    target_link_libraries(lua PUBLIC readline)

    target_compile_definitions(luac PRIVATE "LUA_USE_READLINE")
    target_link_libraries(luac PUBLIC readline)
endif()
