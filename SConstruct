import os, platform
import shutil

spliceEnv = Environment()

def RemoveFolderCmd(target, source, env):
  if os.path.exists(source[0].abspath):
    shutil.rmtree(source[0].abspath)

# define the clean target
if 'clean' in COMMAND_LINE_TARGETS:
  cleanBuild = spliceEnv.Command( spliceEnv.File('cleaning build folder'), spliceEnv.Dir('.build'), RemoveFolderCmd )
  cleanStage = spliceEnv.Command( spliceEnv.File('cleaning stage folder'), spliceEnv.Dir('.stage'), RemoveFolderCmd )
  spliceEnv.Alias('clean', [cleanBuild, cleanStage])
  Return()

# try to setup environment variables from command line arguments
#   with-boost=<dir> sets BOOST_DIR
#   with-fabric=<dir> sets FABRIC_DIR
#   splice-version=<str> sets FABRIC_SPLICE_VERSION
#   debug=0|1 (optional) sets FABRIC_BUILD_TYPE
#   x86=0|1 (optional) sets FABRIC_BUILD_ARCH
if not "FABRIC_DIR" in os.environ:
  fd = ARGUMENTS.get("with-fabric", None)
  if fd and os.path.isdir(fd):
    os.environ["FABRIC_DIR"] = str(os.path.abspath(fd))

if not "FABRIC_SPLICE_VERSION" in os.environ:
  vs = ARGUMENTS.get("splice-version", None)
  if version:
    try:
      l = map(int, vs.split("."))
      os.environ["FABRIC_SPLICE_VERSION"] = "%d.%d.%d" % (l[0], l[1], l[2])
    except:
      pass

if not "FABRIC_BUILD_OS" in os.environ:
  import sys
  if sys.platform == "win32":
    os.environ["FABRIC_BUILD_OS"] = "Windows"
  elif sys.platform == "darwin":
    os.environ["FABRIC_BUILD_OS"] = "Darwin"
  else:
    os.environ["FABRIC_BUILD_OS"] = "Linux"

if not "FABRIC_BUILD_ARCH" in os.environ:
  try:
    os.environ["FABRIC_BUILD_ARCH"] = "x86" if int(ARGUMENTS.get("x86", "0")) != 0 else "x86_64"
  except:
    pass

if not "FABRIC_BUILD_TYPE" in os.environ:
  try:
    os.environ["FABRIC_BUILD_TYPE"] = "Debug" if int(ARGUMENTS.get("debug", "0")) != 0 else "Release"
  except:
    pass

if not "BOOST_DIR" in os.environ:
  bd = ARGUMENTS.get("with-boost", None)
  if bd and os.path.isdir(bd):
    os.environ["BOOST_DIR"] = str(os.path.abspath(bd))

if not "BOOST_LIB_SUFFIX" in os.environ:
  os.environ["BOOST_LIB_SUFFIX"] = str(ARGUMENTS.get("boost-lib-suffix", ""))

# check environment variables
for var in ['FABRIC_DIR', 'FABRIC_SPLICE_VERSION', 'FABRIC_BUILD_OS', 'FABRIC_BUILD_ARCH', 'FABRIC_BUILD_TYPE', 'BOOST_DIR']:
  if not os.environ.has_key(var):
    print 'The environment variable %s is not defined.' % var
    exit(0)
  if var.lower().endswith('_dir'):
    if not os.path.exists(os.environ[var]):
      print 'The path for environment variable %s does not exist.' % var
      exit(0)


if not os.path.exists(spliceEnv.Dir('.stage').abspath):
  os.makedirs(spliceEnv.Dir('.stage').Dir('API').Dir('FabricSpliceAPI').Dir('lib').abspath)

(spliceApiAlias, spliceApiFiles) = SConscript(
  dirs = ['.'],
  exports = {
    'parentEnv': spliceEnv,
    'FABRIC_DIR': os.environ['FABRIC_DIR'],
    'FABRIC_SPLICE_VERSION': os.environ['FABRIC_SPLICE_VERSION'],
    'FABRIC_BUILD_TYPE': os.environ['FABRIC_BUILD_TYPE'],
    'FABRIC_BUILD_OS': os.environ['FABRIC_BUILD_OS'],
    'FABRIC_BUILD_ARCH': os.environ['FABRIC_BUILD_ARCH'],
    'STAGE_DIR': spliceEnv.Dir('.stage').Dir('API').Dir('FabricSpliceAPI'),
    'BOOST_DIR': os.environ['BOOST_DIR'],
    'BOOST_LIB_SUFFIX': os.environ['BOOST_LIB_SUFFIX']
  },
  variant_dir = spliceEnv.Dir('.build')
)

allAliases = [spliceApiAlias]
spliceEnv.Alias('all', allAliases)
spliceEnv.Default(allAliases)
