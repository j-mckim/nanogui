Import('env')

import os
import re

def synthesize_resources_files(target, source, env):
    # Synthesize the target .cxx and .h files, based on the given
    # (source) font files.
    did_this_fail = True
    cxx_target = None
    h_target = None
    # The supplied target is required to be an array of two filenames,
    # a .cxx and a .h. Determine which element is which.
    for t in target:
        if re.search('\.cxx$', t.rstr()):
            cxx_target = t
            pass
        elif re.search('\\.h$', t.rstr()):
            h_target = t
        else:
            print('error: target contains unrecognized file extension,', t.rstr())
            Exit(1)
        pass # for t
    if cxx_target == None or h_target == None:
        print('error: targets lack either .cxx or .h')
        Exit(1)
    ngr_cxx_file = open(cxx_target.get_path(), 'w') # FIXME errcheck
    print('info: writing', cxx_target.get_path())
    ngr_h_file = open(h_target.get_path(), 'w') # FIXME errcheck
    # Synthesize the guard pp macro from the header filename.
    cpp_guard_name = '_%s_' % (re.sub('[^\w]', '_', os.path.basename(h_target.get_path())))
    print(
        '/* Autogenerated by scons */\n'
        '#include <%s>\n' % (os.path.basename(h_target.get_path())) ,
        end = '',
        file = ngr_cxx_file)
    print(
        '/* Autogenerated by scons */\n'
        # '#pragma once\n'
        '#if !defined(%s)\n'
        '#define %s 1\n'
        '#include <cstdint>\n' % (cpp_guard_name, cpp_guard_name),
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
            'extern const uint8_t %s[%d];\n'
            'extern uint32_t %s_size;\n' % (ttf_id, len(ttf_blob), ttf_id),
            end = '',
            file = ngr_h_file)
        print(
            'const uint8_t %s[%d] = {' % (ttf_id, len(ttf_blob)),
            end = '',
            file = ngr_cxx_file)
        for b in ttf_blob:
            print('0x%02x' % (b), end = ',', file = ngr_cxx_file)
        ttf_f.close()
        print(
            '};\n'
            'uint32_t %s_size = sizeof(%s);\n' % (ttf_id, ttf_id),
            end = '',
            file = ngr_cxx_file)
        pass # for ttf_fobj
    print('#endif // %s' % (cpp_guard_name), file = ngr_h_file)
    ngr_cxx_file.close()
    ngr_h_file.close()
    did_this_fail = False
    return did_this_fail
    pass # synthesize_resources_files

env.Command(
    # FIXME - the targets need to be part of the dependencies
    # (i.e. source Node) for the Library(), not hard-coded here.
    target = ['src/nanogui_resources.cxx', 'src/nanogui_resources.h'],
    source = env.Glob('resources/*.ttf') + env.Glob('resources/*.gl'), # FIXME per target architecture
    action = synthesize_resources_files
    )

env.StaticLibrary(
    target = 'nanogui',
    source = ['src/nanogui_resources.cxx'] + env['libnanogui_sources'] # fail, no lib
    # source = env['libnanogui_sources'] # fail, no lib
    # source = ['src/nanogui_resources.cxx'] # fail, no lib
    # source = ['src/xnanogui_resources.cxx'] # works, forced failure
    # source = ['build_Linux_x86_64_debug/src/nanogui_resources.cxx'] # fails, no lib
)

# Local Variables:
# mode: python
# End:
