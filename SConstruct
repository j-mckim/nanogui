# FIXME - config via make-like args
# - (default)
# all
# webasm/emscripten (auto)
# debug

# FIXME - test target

# FIXME - install target should also include a pkg-config spec
# that, in turn, should be used when building the test code

# nb - as an aside, optparse doesn't accept '--no...' form arguments.

# CMakeLists.txt cruft
#
# NANOGUI_VERSION - only used in CMake's project(). Could use this for
# lib version, otherwise a nonce
#
# CMAKE_BUILD_TYPE - this can be one of 'Debug', 'Release',
# 'MinSizeRel', or 'RelWithDebInfo'. It's not used, except to add
# flags '-O3 -DNDEBUG' if REL.
#
# CMAKE_CONFIGURATION_TYPE - not used.
#
# NANOGUI_MASTER_PROJECT - if True, and the python module is being
# built, also builds docs.
#
# WIN32 (intrinsic) - sets NANOGUI_BUILD_GLAD_DEFAULT, adds library
# opengl32
#
# NANOGUI_BUILD_GLAD - defaults to NANOGUI_BUILD_GLAD_DEFAULT. Adds
# glad.c to the sources. For the shared lib, adds a couple CPP
# definitions, GLAD_GLAPI_EXPORT, GLAD_GLAPI_EXPORT_BUILD. And a bit
# more (FIXME elaborate). - nb - for win32
#
# NANOGUI_BUILD_GLFW - runs a chunk of code at #217. It appears this
# compiles glfw source and adds the objects to the nanovg library.
#
# NANOGUI_BUILD_SHARED - NB - if True, add CPP flags NANOGUI_SHARED,
# NVG_SHARED. (FIXME verify these are actually used in the source
# code)
#
# NANOGUI_BUILD_EXAMPLES - runs a chunk of code at #554. QED.
#
# NANOGUI_BUILD_PYTHON - runs a chunk of code at #571. (FIXME - impl)
#
# From cmake webasm typescript:
# OK NANOGUI_BUILD - should be defined
# NANOGUI_USE_GLES - should be defined
# NANOGUI_GLES_VERSION=2 - FIXME both gles2 and gles3 are available in em
# or (better) - probe em to find out which is avialable

# From notes-webasm.org
#
# TLDR - Along with specifying '-pthread', have the compiler arrange
# for creating the threads as they are declared, rather than having it
# done dynamically. The latter cannot be done reliably. There are many
# spots where the process of running threads can fail and be
# mysterious about it, leading to lots of headaches.

# Specify -sPTHREAD_POOL_SIZE=<number of threads> using the number of
# threads the app requires.

# Specify -sPTHREAD_POOL_SIZE_STRICT=2. This causes the webasm app to
# signal a failure in thread creation by throwing an event.

# Specify '-fwasm-exceptions' to cause the compiler to emit efficient
# (wasm) exception handling code.

# Specifying '-pthreads' causes the cpp macro '
# __EMSCRIPTEN_PTHREADS__' to be defined.

# Emulation of OpenGL ES 2.0/3.0 (via '-s FULL_ES2=1', '-s
# FULL_ES3=1') should be avoided unless necessary. The default
# provides a WebGL-friendly subset of OpenGL ES 2.0/3.0.

# Specifying '--emrun' causes extra code to be included to facilitate
# the test environment. Presumably tunneling stderr and stdout back to
# the invoking terminal. Omit it for production.

# -sMAX_WEBGL_VERSION=2 to enable WebGL 2
# -sMIN_WEBGL_VERSION=1 to drop support for WebGL 1
# -sFULL_ES3 to enable all GLES3 features, not nust WebGL2
#
# -fsanitize=undefined to enable the clang undefined behavior sanitizer.
#
# These are described in the script ~/src/settings.js:
#
# -flto - link time optimization, for apps


# FIXME testing
# libcester - header only, embedded, depends on nothing, current
#  cester.h
# tau
#  ~/src/tau

import os
import platform
from pprint import pprint # FIXME for debugging

# pkg-config elements

# install work
# - libraries
# - header
# - pkg-config info
#
# prefix=
# exec_prefix=
# libdir=
# includedir=
# Name:
# Description:
# Version:
# URL:
# Requires: (n/a)
# Requires.private: (n/a)
# Conflicts: (n/a)
# Libs:
# Libs.private:
# Cflags:
#
# cmake find module (if separate from pkg-config)
# LIBNANO_INCLUDE_DIRS
# LIBNANO_LIBRARIES
# LIBNANO_DEFINITIONS
# LIBNANO_LIBRARY_DIRS
#
# git remote show origin [to fetch url]
#
# wrt emscripten ports, there is no obvious place to put external
# packages (emscripten calls these 'ports').

# FIXME package builder?

# Run in parallel by default.
if False and not GetOption('num_jobs'): # FIXME temp omit
    # FIXME - this doesn't work because scons 'helpfully' provides a
    # default 'num_jobs' if it isn't specified by the user. There may
    # not be a nice way to test for this. The ugly way would be to
    # check the raw command line for '-j' or '--jobs' and also check
    # the environment for $NUM_CPU.
    #
    # not ('-j' in sys.argv or '--jobs' in sys.argv or 'NUM_CPU' in os.environ)
    SetOption('num_jobs', len(os.sched_getaffinity(0)))
print('info: parallel builds:', GetOption('num_jobs'))

# FIXME option --prefix? (for install)

# Determine which library targets need to be built. By default, all.
AddOption(
    '--xdebug',
    action = 'store_true',
    help = 'Build debug version(s).',
)
AddOption(
    '--xrelease',
    action = 'store_true',
    help = 'Build release version(s).',
)
AddOption(
    '--xwebasm',
    action = 'store_true',
    help = 'Build WebAsm version(s).',
)
AddOption(
    '--xnative',
    action = 'store_true',
    help = 'Build native version(s).',
)
build_debug = False
build_release = False
if GetOption('xdebug'):
    build_debug = True
    if GetOption('xrelease'):
        build_release = True
else:
    if GetOption('xrelease'):
        build_release = True
    else:
        build_debug = True
        build_release = True
build_native = False
build_webasm = False
if GetOption('xnative'):
    build_native = True
    if GetOption('xwebasm'):
        build_webasm = True
else:
    if GetOption('xwebasm'):
        build_webasm = True
    else:
        # neither true
        build_native = True
        build_webasm = True


# source files
libnanogui_example_files = [
    'src/example1.cpp',
    'src/example2.cpp',
    'src/example3.cpp',
    'src/example4.cpp',
    'src/example_icons.cpp',
    ]
libnanovg_source_files = [
    'ext/nanovg/src/nanovg.c'
]
libnanogui_source_files = [
    'src/button.cpp',
    'src/canvas.cpp',
    'src/checkbox.cpp',
    'src/colorpicker.cpp',
    'src/colorwheel.cpp',
    'src/combobox.cpp',
    'src/common.cpp',
    'src/graph.cpp',
    'src/imagepanel.cpp',
    'src/imageview.cpp',
    'src/label.cpp',
    'src/layout.cpp',
    'src/messagedialog.cpp',
    'src/opengl.cpp',
    'src/popupbutton.cpp',
    'src/popup.cpp',
    'src/progressbar.cpp',
    'src/renderpass_gl.cpp',
    'src/screen.cpp',
    'src/shader.cpp',
    'src/shader_gl.cpp',
    'src/slider.cpp',
    'src/tabwidget.cpp',
    'src/textarea.cpp',
    'src/textbox.cpp',
    'src/texture.cpp',
    'src/texture_gl.cpp', # FIXME only Linux, not OSX?
    'src/theme.cpp',
    'src/traits.cpp',
    'src/vscrollpanel.cpp',
    'src/widget.cpp',
    'src/window.cpp',
]

env_common = Environment(
    CCFLAGS = [
        '-Wall',
        '-Wextra',
    ],
    CPPDEFINES = [
        'NANOGUI_BUILD',
        ],
    CPPPATH = [
        '#include',
        '#ext/nanovg/src',
        'src',
    ],
    CXXFLAGS = [
        # '-std=c++17',
        '-std=c++2a',
        '-Wall',
        '-Wextra',
    ],
    LINKFLAGS = [
        '-Wl,--demangle', # Useful in building apps
    ],
)

# env_common = Environment(FIXME-defaults)
# FIXME do Configure() checks here (if any)
# conf = Configure(env)
# conf.CheckCHeader()
# conf.CheckFunc()
# conf.CheckLib()
# conf.CheckLibWithHeader()
# conf.CheckType()
# glfw3
# stdint.h?
# pybind11?
# string.h (memset) - better choice?
# env_common = conf.Finish()

if build_native:
    # FIXME both shared and static
    env_native = env_common.Clone()
    platform_system = platform.system()
    target_name = platform_system + '_' + platform.machine()
    if platform_system == 'Darwin':
        # FIXME add libs suitable for OSX
        CPPDEFINES = [
            'NANOGUI_USE_METAL',
        ],
        # Libraries needed only for building examples.
        LIBS = [
            # 'GL',
            # 'glfw',
            # 'pthread',
        ],
        # FIXME add libs suitable for MSWin
        if build_debug:
            print('info: building native debug target', target_name)
            env = env_native.Clone()
            # FIXME impl
            pass # build_debug
        pass # Windows
        if build_release:
            print('info: building native release target', target_name)
            print('warning: build_release not implemented')
            env = env_native.Clone()
            # FIXME impl
            pass # build_release
        pass # Darwin
    elif platform_system == 'Linux':
        # env_native.ParseConfig('pkg-config --cflags --libs gl')
        # env_native.ParseConfig('pkg-config --cflags --libs glfw3')
        env_native.AppendUnique(
            CPPDEFINES = [
                'NANOGUI_USE_OPENGL',
                ],
            # Libraries needed only for building examples.
            LIBS = [
                'GL', # FIXME pkg-config --libs gl
                'glfw', # FIXME pkg-config --libs glfw3
                'pthread',
            ],
        )
        if build_debug:
            print('info: building native debug target', target_name)
            env = env_native.Clone()
            env.AppendUnique(
                # FIXME - some of the below flags are common to both debug
                # and release. Some are common to both local and webasm.

                # FIXME - how many of these are needed? Deprecate the
                # rest. Move the bunch up to the common base environment.
                CPPDEFINES = [
                    # Define if building GLFW and as part of a shared
                    # library. It appears to be
                    # Microsoft-compiler-specific. [copy CMake]
                    '_GLFW_BUILD_DLL',

                    # Referenced in common.h [copy CMake]
                    'NANOGUI_BUILD',

                    # Referenced in opengl.h, common.h [copy CMake]
                    'NANOGUI_SHARED',

                    # Per target architecture, this or one if its kin
                    # (GLES, METAL) must be defined
                    # 'NANOGUI_USE_OPENGL',

                    # Referenced in opengl_check.h, screen.cpp,
                    # renderpass_gl.cpp, renderpass_metal.mm [copy CMake
                    # and/or enable for debug but not for
                    # production]. Appears to enable additional error
                    # checking; macro CHK(), enables flag NVG_DEBUG. Flag
                    # NVG_DEBUG does not appear to be used; a nonce.
                    #
                    # nb - some of the error checking involves throwing an
                    # exception, so exceptions should be enabled for the
                    # webasm target.
                    'NDEBUG',

                    # Not used?
                    'NVG_BUILD',

                    # Not used?
                    'NVG_SHARED',

                    # Not used?
                    'NVG_STB_IMAGE_IMPLEMENTATION',
                ],
                # CPPPATH = ['#include', '#ext/nanovg/src', 'src'],
                CCFLAGS = ['-g', '-Og'],
                CXXFLAGS = ['-g', '-Og'],
            )
            variant_dir = 'build_' + target_name + '_debug'
            env['libnanogui_sources'] = libnanovg_source_files + libnanogui_source_files
            env['nanogui_example_sources'] = libnanogui_example_files
            env.SConscript(
                'SConscript',
                src_dir = '.',
                variant_dir = variant_dir,
                exports = 'env',
                duplicate = False,
                must_exist = True)
            pass # build_debug
        if build_release:
            print('info: building native release target', target_name)
            print('warning: build_release not implemented')
            env = env_native.Clone()
            env.AppendUnique(
                CCFLAGS = ['-O3'],
                CXXFLAGS = ['-O3'],
            )
            variant_dir = 'build_' + target_name + '_release'
            # FIXME impl
            # FIXME env = env_native.Clone(...)
            # VariantDir('build-xxx', 'src', duplicate = False)
            # env.Program([FIXME-sources])
            # env.Install(FIXME)
            # env.InstallVersionedLib(FIXME)
            # env.Package(FIXME)
            pass # build_release
        # FIXME - finally, build python module
        pass # Linux
    elif platform_system == 'Windows':
        CPPDEFINES = [
            'NANOGUI_USE_GLES',
        ],
        LIBS = [ # Libraries needed for building examples.
        ],
        if build_debug:
            print('info: building native debug target', target_name)
            env = env_native.Clone()
            # FIXME impl
            pass # build_debug
        if build_release:
            print('info: building native release target', target_name)
            print('warning: build_release not implemented')
            env = env_native.Clone()
            # FIXME impl
            pass # build_release
        pass # Windows
    else:
        print('warning: cannot build target for unrecognized platform,', platform_system)
        pass
if build_webasm:
    # FIXME only static

    # FIXME - flags of interest
    # -O0 (debug)/-O3 (release) [maybe -Oz]
    # -s ...=... (FIXME - get this from src/settings.js)
    #  ASSERTIONS=0 (release)
    #  RUNTIME_LOGGING=1 (debug)
    #  DISABLE_EXCEPTION_CATCHING=0
    #  NODEJS_CATCH_EXIT=0
    #  FIXME - throw/catch support?
    #  FIXME - GL support (version)?
    #  FIXME - threads? (-pthread)
    # --tracing (?)
    # -flto (production)
    # --closure=1

    target_name = 'emscripten_webasm'
    # The method preferred by the emscripten compiler developers for
    # determining the location of all the relevant executables is to
    # find the path to one, then prepend it to all the others.
    emcxx_path = WhereIs('em++')
    if emcxx_path:
        em_root = os.path.dirname(emcxx_path)
        print("info: using emscripten suite from '%s'" % em_root);
        env_webasm = env_common.Clone(
            AR = os.path.join(em_root, 'emar'),
            CC = os.path.join(em_root, 'emcc'),
            CXX = os.path.join(em_root, 'em++'),
            RANLIB = os.path.join(em_root, 'emranlib'),
        )
        env_webasm.AppendUnique(
            # Libraries needed for building examples.
            CCFLAGS = [
                '-fsanitize=undefined',
            ],
            CXXFLAGS = [
                '-fsanitize=undefined',
            ],
            CPPDEFINES = [
                # Referenced in common.h [copy CMake]
                'NANOGUI_BUILD',
                
                # Referenced in opengl.h, common.h [copy CMake]
                # 'NANOGUI_SHARED',
                
                'NANOGUI_USE_OPENGL',
                
                'NDEBUG', # FIXME move to build_debug
                
                # Not used?
                'NVG_BUILD',
                
                # Not used?
                'NVG_STB_IMAGE_IMPLEMENTATION',
            ],
            # CPPPATH = ['#include', '#ext/nanovg/src', 'src'],
            LINKFLAGS = [
                '--emrun',
                '-fsanitize=undefined',
                '-g',
                '-pthreads',
                '-sLLD_REPORT_UNDEFINED',
                '-sMIN_WEBGL_VERSION=2',
                '-sMAX_WEBGL_VERSION=2',
                '-sPTHREAD_POOL_SIZE=2',
                '-sPTHREAD_POOL_SIZE_STRICT=2',
                '-sUSE_GLFW=3',
                ],
            
            # FIXME - emscripten implicitly adds libraries GL and
            # glfw, despite my omitting them here. It further seems to
            # ignore _any_ libraries I have specified here. This might
            # be a scons issue; scons is using 'em++' to do the link.
            # There is no specific 'emld' command in the emscripten
            # suite. Libraries can be added via '-lxxx' in LINKFLAGS
            # above. For the most part, emscripten magically figures
            # out which libraries are needed. Raises the question: If
            # I wish to have a private library available for my use,
            # how best do I cause emscripten to use it?
            #
            # Two undefined symbols occur, in both example1 and
            # example4:
            #
            # glDrawBuffer
            # glTexImage2DMultisample
            #
            # glDrawBuffer may be present in libGL.a
            # (~/upstream/emscripten/cache/sysroot/lib/wasm64-emscripten/libGL.a)
            #
            # glTexImage2DMultisample is declared in GL/glext.h but
            # doesn't appear in any library (via fgrep).
            
            LIBS = [
                # 'GL',
                # 'glfw',
                # 'XYZZYglfw3', # FIXME ignored
                # 'pthread',
                # 'SDL2',
            ], # FIXME probably not right; review cmake script
        )
        variant_dir = 'build_webasm_emscripten'
        if build_debug:
            print('info: building webasm debug target', target_name)
            # print('warning: webasm build_debug not implemented')

            # FIXME name example outputs with extension .html. That
            # would be: appending .html to the executable name. Not
            # exactly straightforward, and would have to be some more
            # exception code in the SConscript.
            
            env = env_webasm.Clone()
            env['libnanogui_sources'] = libnanovg_source_files + libnanogui_source_files
            env['nanogui_example_sources'] = libnanogui_example_files
            env['emrun_friendly'] = True
            env.SConscript(
                'SConscript',
                src_dir = '.',
                variant_dir = variant_dir,
                exports = 'env',
                duplicate = False,
                must_exist = True)
            pass # build_debug
        if build_release:
            print('info: building webasm release target', target_name)
            print('warning: webasm build_release not implemented')
            # FIXME env = env_webasm.Clone(...)
            pass # build_release
    else:
        print('warning:', target_name, 'target will not be built; compiler (em++) not found')
    pass # build_webasm

# FIXME - also via COMMAND_LINE_TARGETS
# https://scons.org/doc/production/HTML/scons-user/ch10s03.html
# This needs more investigation - Each of these have must be
# enumerated as a bona-fide target (Program, Library, etc.)?
# env.Alias() to create a named target

# cmake's flags, native, from 'make --trace'
#
# glfw (c)
# /usr/bin/cc
# -D_GLFW_BUILD_DLL
# -D_GLFW_USE_CONFIG_H
# -I/home/jim/src/nanogui/ext/glfw/include
# -I/home/jim/src/nanogui/ext/glfw/src
# -I/home/jim/src/nanogui/build-cmake-2/ext/glfw/src
# -O3
# -DNDEBUG
# -fPIC
# -march=nehalem
# -Wall
# -fvisibility=hidden	### recommended for shared objects, better link time
# -Wdeclaration-after-statement
# -o CMakeFiles/glfw_objects.dir/context.c.o
# -c /home/jim/src/nanogui/ext/glfw/src/context.c
#
# nanogui
# /usr/bin/c++
# -DNANOGUI_BUILD	### not used?
# -DNANOGUI_SHARED	### not used?
# -DNANOGUI_USE_OPENGL	### shader.h, screen.cpp, many others
# -DNVG_BUILD		### nanovg.h
# -DNVG_SHARED		### nanovg.h
# -DNVG_STB_IMAGE_IMPLEMENTATION	### not used?
# -D_GLFW_BUILD_DLL	### glfw3.h, x11_init.c, others
# -I/home/jim/src/nanogui/build-cmake-2
# -I/home/jim/src/nanogui/include
# -I/home/jim/src/nanogui/ext/nanovg/src
# -I/home/jim/src/nanogui/ext/glfw/include
# -Wall
# -Wextra
# -O3
# -DNDEBUG		### opengl_check.h, screen.cpp, renderpass_gl.cpp
# -flto			### run standard link-time optimizer (release)
# -fno-fat-lto-objects	### omit, dubious
# -fPIC
# -fvisibility=hidden
# -march=nehalem
# -std=gnu++17
# -o CMakeFiles/nanogui.dir/src/checkbox.cpp.o
# -c /home/jim/src/nanogui/src/checkbox.cpp

# cmake's flags, wasm, from 'make --trace'
#
# glfw
# home/jim/src/emsdk/upstream/emscripten/emcc
# -DNANOGUI_BUILD
# -DNANOGUI_GLES_VERSION=2	### opengl.h, scren.cpp, renderpass_gl.cpp, others
# -DNANOGUI_USE_GLES	### opengl.h, renderpass.h, shader.h, texture.h, others
# -DNVG_STB_IMAGE_IMPLEMENTATION
# @CMakeFiles/nanogui.dir/includes_C.rsp
#  -I/home/jim/src/nanogui/build-cmake
#  -I/home/jim/src/nanogui/include
#  -I/home/jim/src/nanogui/ext/nanovg/src
# -O3
# -DNDEBUG
# -O3
# -DNDEBUG
# -march=nehalem
# -o CMakeFiles/nanogui.dir/ext/nanovg/src/nanovg.c.o
# -c /home/jim/src/nanogui/ext/nanovg/src/nanovg.c
#
# nanogui
# /home/jim/src/emsdk/upstream/emscripten/em++
# -DNANOGUI_GLES_VERSION=2
# -DNANOGUI_USE_GLES
# @CMakeFiles/example_icons.dir/includes_CXX.rsp
# -O3
# -DNDEBUG
# -fvisibility=hidden
# -O3
# -DNDEBUG
# -march=nehalem
# -std=gnu++17
# -o CMakeFiles/example_icons.dir/src/example_icons.cpp.o
# -c /home/jim/src/nanogui/src/example_icons.cpp

Help("""
The default is to try to build all the above versions.
""", append = True)

# For local (vs. webasm) builds
# env = Environment()
# conf = Configure(env)
## Checks for libraries, header files, etc. go here!
# conv.CheckCHeader('math.h') # etc.
## CheckFunc()
## CheckLib()
## CheckType()
# env = conf.Finish()

# Local Variables:
# mode: python
# End:
