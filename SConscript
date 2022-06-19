Import('env')

import os
import re

def synthesize_resources_files(target, source, env):
    # Synthesize the target nanogui_resources.[cxx, h] files, based on
    # the given font files. nb - 'targets' is ignored; output
    # filenames are hard-coded.
    did_fail = True
    
    print('DEBUG: (all) source', source)
    # source contains the four FS objects; resources/*.ttf.
    
    print('DEBUG: (all) target', target)
    # target contains two FS objects;
    # build_.../nanogui_resources.[cxx, h]

    cxx_file = None
    h_file = None
    # The supplied target is required to be an array of two filenames,
    # a .cxx and a .h. Determine which element is which.
    for t in target:
        # print('XXXX (one) target', t.get_path()) # works
        if re.search('\.cxx$', t.rstr()):
            cxx_file = t
            pass
        elif re.search('\\.h$', t.rstr()):
            h_file = t
            pass
        else:
            print('error: targets contains unrecognized file extension,', t.rstr())
            Exit(1)
            pass
        pass # for t
    if cxx_file == None or h_file == None:
        print('error: targets lack either .cxx or .h')
        Exit(1)
        pass
        # print('XXXX target', t.variant_dirs())
    # Exit()

    # FIXME this technique, here, triggers an scons diagnostic
    # "Multipe ways sto build the same target ..."
    # env.Textfile(target = cxx_file, source = [ 'FIXME - cxx code' ])
    # env.Textfile(target = h_file, source = [ 'FIXME - h code' ])

    # FIXME - works as expected, places in working directory, not
    # target directory.
    # env.Textfile(target = 'FIXME_cxx_file', source = [ 'FIXME\n - cxx code\n' ])

    # FIXME - these nanogui_resources.* are being created in the wrong
    # place; the invocation directory rather than the variant
    # directory.
    #
    # Could make it as a FS object then get its rstr value. Perhaps it
    # needs to be fetched from the target arg.
    #
    # FIXME - what needs to happen is the resources files need to be
    # copied to the build directory?
    
    # FIXME - as noted above, these two open()s need to be relative to
    # the target directory. Can't think of a particularly nice way to
    # do this. Target by definition is two files, source code and a
    # header. Ideally, they would come from the target objects, rather
    # than be hard-coded. That way, they will be placed in the correct
    # directory.
    #
    # The thoughtful user will set the target to two files, a .cxx and
    # a .h. If we assume that, we can extract them here with a little
    # fiddling (search for a .h and assume the other is the .cxx,
    # maybe). If no .h specified, punt (error out).
    #
    # Odd thought: is it possible to supply a dictionary for the
    # target? Then we could associate keywords ('header', 'code') with
    # the pertinent output files.

    # FIXME - is there a way to open the target (objects) directly?
    # via rstr(), they only show as simple file names; no paths. Just
    # opening the rstr isn't going to put them in the correct
    # directory. What happens if I Touch() them?

    # env.Substfile()?
    # env.Textfile()?
    # can a File object be just a chunk of text?
    #
    # - deprecate explicit open()
    # - Separate the .cxx and .h from the targets
    # - For each, a Textfile() with an array containing a single
    #   string.

    # ngr_cxx_file = open('nanogui_resources.cxx', 'w') # FIXME errcheck
    ngr_cxx_file = open(cxx_file.get_path(), 'w') # FIXME errcheck
    # print('info: writing', ngr_cxx_file.name())
    print('info: writing', ngr_cxx_file)

    # ngr_h_file = open('nanogui_resources.h', 'w') # FIXME errcheck
    ngr_h_file = open(h_file.get_path(), 'w') # FIXME errcheck
    cpp_guard_name = '_nanogui_resources_h_'
    print(
        '/* Autogenerated by scons */\n\n#include <cstdint>\n\n' ,
        end = '',
        file = ngr_cxx_file)
    print(
        '/* Autogenerated by scons */\n#pragma once\n#if !defined(%s)\n#define %s 1\n#include <cstdint>\n' % (cpp_guard_name, cpp_guard_name),
        end = '',
        file = ngr_h_file)
    for ttf_fobj in source:
        ttf_fname = ttf_fobj.rstr()
        # Synthesize a lexically correct C++ identifier from the
        # font's filename.
        ttf_id = re.sub('[^\w]', '_', os.path.basename(ttf_fname).lower())
        print('info: processing font file', ttf_fname, 'as', ttf_id)
        ttf_f = open(ttf_fname, mode = 'rb') # FIXME errcheck
        ttf_blob = ttf_f.read()
        print(
            'extern const uint8_t %s[%d];\nextern uint32_t %s_size;\n' % (ttf_id, len(ttf_blob), ttf_id),
            end = '',
            file = ngr_h_file)
        print(
            'extern const uint8_t %s[%d] = {' % (ttf_id, len(ttf_blob)),
            end = '',
            file = ngr_cxx_file)
        for b in ttf_blob:
            # print(hex(b), end=',') # nb - not formatted
            print('0x%02x' % (b), end = ',', file = ngr_cxx_file)
            pass
        ttf_f.close()
        print(
            '};\nuint32_t %s_size = sizeof(%s);\n' % (ttf_id, ttf_id),
            end = '',
            file = ngr_cxx_file)
        pass # for ttf_fname
    print('#endif // %s' % (cpp_guard_name), end = '', file = ngr_h_file)
    ngr_h_file.close()
    ngr_cxx_file.close()
    did_fail = False
    return did_fail
    pass # synthesize_resources_files

# fixme_sources = [ 'src/button.cpp' ]
#print(env.Dump()) # ; Exit()
#print('XXXX in sub')
#env.Library('nanogui', fixme_sources)
# xxx = env['libnanogui_sources']

# FIXME - prior to compile, need th have nanogui_resources.h in
# include path. That means it must be (1) generated and (2) either put
# in a path or path modified to find it.
#
# FIXME - its corresponding .cxx file must also be compiled and added
# to the library. Depending on the architecture, this file will
# change, but that action should be isolated in the function that
# synthesizes the file. Both the .cxx and the .h files should only
# exist in the build directories, and should be cleaned.
#
# FIXME - move the synthesize function to this SConscript? It isn't
# needed elsewhere.

# FIXME - not going into the variant directory, rather the cwd

# FIXME - fails to include any font files. Glob returns four FS.File
# objects; there are four .ttf files.
# xsource = env.Glob('resources/*.ttf')
# print('xs', xsource); Exit()

env.Command(
    # FIXME - the targets need to be part of the dependencies
    # (i.e. source Node) for the Library(), not hard-coded here.
    target = ['nanogui_resources.cxx', 'nanogui_resources.h'],
    source = env.Glob('resources/*.ttf'), # FIXME per target architecture

    # FIXME temp omit
    action = synthesize_resources_files

    # nb - this works for creating at all the target files in the
    # correct directory. nb(2) - the docs may incorrectly depict these
    # variables in lower case; they must be upper case.
    # https://scons.org/doc/HTML/scons-design/ch04s02.html
    # action = ["/bin/touch $TARGETS"]
    
    # action = ["/bin/echo target $TARGET"]
    # action = ["/bin/echo source $SOURCES"],
    # action = ["/bin/echo target $TARGET"]
    )

# FIXME this fails with a confusing diagnostic:
# "[build_Linux_x86_64_debug/nanogui_resources.o] AttributeError :
# 'NoneType' object has no attribute 'get_contents'". Make the sources
# required? This is only a test.

# env.Library('nanogui', ['nanogui_resources.cxx']) # FIXME temp, testing

# env.Library('nanogui', env['libnanogui_sources'])

# Local Variables:
# mode: python
# End:
