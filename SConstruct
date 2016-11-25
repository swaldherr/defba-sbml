import SCons.Script
import os
from collections import defaultdict

docbase = '/Users/waldherr/Documents'

task_targets = defaultdict(lambda: [])

def add_targets(target, source, env):
    t = os.path.basename(str(target[0]))[:-4]
    target.extend([os.path.join("results", t, i) for i in task_targets[t]])
    return target, source
bld = Builder(action = "python run.py --task $TARGET --log --export=png --memoize",
                 suffix = '.log',
                 src_suffix = '.py',
                 emitter = add_targets)

env = Environment(ENV={"TEXINPUTS":os.environ.get("TEXINPUTS",""), "HOME":os.environ["HOME"], "PATH":os.environ["PATH"],
		       "TARFLAGS":"-c -z",},
    		  BUILDERS = {'Pytask' : bld})

texdocs = Glob("docs/*.tex")
docbuilds = []
for f in texdocs:
    docbuilds.append(env.PDF(target=str(f)[:-4]+".pdf", source=f))

# define builder for sbml models
def build_sbml(source, target, env, for_signature):
    bname = os.path.basename(target[0].get_path())
    antimonyout = bname[:bname.rfind(".xml")] + "_" + bname[:bname.rfind(".xml")] + "_sbml.xml"
    return "antimony2sbml '%s'; mv '%s' '%s'" % (source[0], antimonyout, target[0])
env.sbml = SCons.Script.Builder(generator = build_sbml, suffix = '.xml', src_suffix='.txt')
env.Append(BUILDERS = {'sbml' : env.sbml})

# define models
models = Glob("examples/*.txt")
for i in models:
    a = env.sbml(i)

