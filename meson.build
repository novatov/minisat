project('minisat', 'cpp',
  version : '0.1',
  default_options : ['warning_level=3'])

src = [
  'minisat/core/Solver.cc',
  'minisat/simp/SimpSolver.cc',
  'minisat/utils/Options.cc',
  'minisat/utils/System.cc',
]

zdep = dependency('zlib')

lib = library('minisat', src,
  install : true, dependencies : zdep)

exe_core = executable('minisat_core', 'minisat/core/Main.cc',
  install : true, link_with : lib, dependencies : zdep)
exe_simp = executable('minisat_simp', 'minisat/simp/Main.cc',
  install : true, link_with : lib, dependencies : zdep)

install_headers(
  'minisat/core/Dimacs.h',
  'minisat/core/Solver.h',
  'minisat/core/SolverTypes.h',
  'minisat/mtl/Alg.h',
  'minisat/mtl/Alloc.h',
  'minisat/mtl/Heap.h',
  'minisat/mtl/IntMap.h',
  'minisat/mtl/IntTypes.h',
  'minisat/mtl/Map.h',
  'minisat/mtl/Queue.h',
  'minisat/mtl/Rnd.h',
  'minisat/mtl/Sort.h',
  'minisat/mtl/Vec.h',
  'minisat/mtl/XAlloc.h',
  'minisat/simp/SimpSolver.h',
  'minisat/utils/Options.h',
  'minisat/utils/ParseUtils.h',
  'minisat/utils/System.h',
  preserve_path : true,
)
