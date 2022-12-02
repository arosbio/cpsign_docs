
(gensign)=

# `gensign`

The `gensign` program is not something that is normally part a workflow, but a way to validate the resulting signature descriptors for
molecules without starting any heavy jobs.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage menu can be retrieved by running command:

```text
> java -jar cpsign-[version].jar gensign


                                         gensign
SYNOPSIS
------------------------------------------------------------------------------------------
  gensign [options]
  gensign @/tmp/runconfigs/parameters.txt [options]
  gensign @C:\Users\User\runconfigs\parameters.txt [options]


DESCRIPTION
------------------------------------------------------------------------------------------
  Gensign generates the signatures for given molecules. Signature generation is automatic
  within CPSign when running all other programs, this program is only intended to give the
  user a grasp of how signature descriptors will be generated and how signature heights
  affect the generated descriptors.


OPTIONS
------------------------------------------------------------------------------------------
  Input:
    -sm | --smiles                           [SMILES]
       SMILES string to generate signatures of
    -i  | --input                            [format] [opt args] [URI | path]
       Path or URI to a SDF, CSV or json-file

  Signature generation:
    -hs | --height-start                     [integer]
       Signatures start height
       Default: 1
    -he | --height-end                       [integer]
       Signatures end height
       Default: 3
    -sg | --signatures-generator             [id | text]
       Type of signatures that should be used, note that stereo-signatures take much
       longer time to compute. Stereo signatures also requires input data to have stereo
       information explicitly given in the file. Options:
         (1) default | normal
         (2) stereo (experimental mode)
       Default: 1

  Output:
    -of | --output-format                    [id | text]
       Output format of predictions, options:
         (1) json
         (2) smiles | plain
         (3) sdf | sdf-v2000
         (4) sdf-v3000
       Default: 1
    -o  | --output                           [path]
       File to write output to (default is printing to screen)
    --output-inchi
       Generate InChI and InChIKey in the output
    --compress
       If the outputfile should be compressed (only possible when writing to file)

  General:
  * --license                                [URI | path]
       Path or URI to license file
    -h  | --help | man
       Get help text
    --short
       Use shorter help text (used together with the --help argument)
    --logfile                                [path]
       Path to a user-set logfile, will be specific for this run
    --silent
       Silent mode (only print output to logfile)
    --echo
       Echo the input arguments given to CPSign
    --seed                                   [integer]
       Set this flag if an explicit RNG seed should be used in tasks that require a RNG
       (randomization of training data, splitting in cross-validation, learning algorithms
       etc). Not used by all programs.
    --progress-bar
       Add a Progress bar in the system error output
    --progress-bar-ascii
       Add a Progress bar in ASCII in the system error output
    --time
       Print wall-time for all individual steps in execution

------------------------------------------------------------------------------------------
```

Gensign requires a valid Standard or PRO license as stated in the usage manual, but it will also require to use **--smiles**, **--input** or both
flags.

## Example Usage

```bash
> java -jar cpsign-[version].jar gensign \
   --smiles CC(=O)NCCC1=CNc2c1cc(OC)cc2 \
   --license /path/to/Standard-license.license

Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Starting signature generation..
   {
     "molecule": {
             "SMILES": "CC(=O)NCCC1=CNc2c1cc(OC)cc2"
     },
     "generatedSignatures": {
             "[N]([C]([C])[C]([C]=[O]))": 1.0,
             "[C](p[C](p[C](p[C,0][O]))p[C]([C]([C])p[C](p[N,1]))p[C](p[C](p[C,0])p[N,1]))": 1.0,
             "[C](p[C]([C]p[C])p[N](p[C]))": 1.0,
             "[C]([C][N])": 1.0,
             "[C](p[C](p[C])p[C](p[C])[O]([C]))": 1.0,
             "[C]([C][N]([C]([C]))=[O])": 1.0,
             "[C]([O]([C](p[C]p[C])))": 1.0,
             "[C]([C]([C])p[C](p[N])p[C](p[C]p[C]))": 1.0,
             "[O]([C][C](p[C](p[C])p[C](p[C])))": 1.0,
             "[C]([C]p[C]p[C])": 1.0,
             "[C]([C]([N])[C](p[C]p[C]))": 1.0,
             "[N]([C]([C]([C]))[C]([C]=[O]))": 1.0,
             "[C](p[C](p[C])p[C](p[C][O]))": 1.0,
             "[C](p[C]p[C]p[N])": 1.0,
             "[O](=[C]([C][N]))": 1.0,
             "[N]([C][C])": 1.0,
             "[O]([C][C](p[C]p[C]))": 1.0,
             "[C](p[C]p[C])": 3.0,
             "[O](=[C])": 1.0,
             "[C](p[C](p[C](p[C,0]))p[C](p[C](p[C]p[C,0]))[O]([C]))": 1.0,
             "[C]([O])": 1.0,
             "[C]([C][C])": 1.0,
             "[C](p[C](p[C]p[C])p[C](p[C][O]))": 1.0,
             "[C]([C])": 1.0,
             "[C]([O]([C]))": 1.0,
             "[N](p[C](p[C]([C]p[C,0]))p[C](p[C](p[C])p[C,0](p[C])))": 1.0,
             "[C](p[C](p[C])p[C]([C]p[C])p[C](p[C]p[N]))": 1.0,
             "[C](p[C](p[C](p[C,0]p[N]))p[C](p[C](p[C,0])[O]([C])))": 1.0,
             "[C](p[C](p[C](p[C,0]))p[C](p[C](p[C,0])p[C]([C]p[C,1]))p[N](p[C,1]))": 1.0,
             "[C]([C]([N]=[O]))": 1.0,
             "[C](p[C](p[C])p[C](p[C]p[C])p[N](p[C]))": 1.0,
             "[C]([C]([N]([C]))[C](p[C](p[N])p[C](p[C]p[C])))": 1.0,
             "[C](p[C]p[C][O])": 1.0,
             "[O]([C][C])": 1.0,
             "[C]([C]([C]([N]))p[C](p[N](p[C,0]))p[C](p[C](p[C])p[C,0](p[C])))": 1.0,
             "[C]([C][N]=[O])": 1.0,
             "[N](p[C]p[C])": 1.0,
             "[O](=[C]([C][N]([C])))": 1.0,
             "[C]([C]([C])[N]([C]))": 1.0,
             "[C]([C]([N]([C])=[O]))": 1.0,
             "[C]([C][N]([C])=[O])": 1.0,
             "[C](p[C](p[C](p[C,0])[O]([C]))p[C](p[C]([C]p[C])p[C](p[C,0]p[N])))": 1.0,
             "[C](p[C]p[N])": 1.0,
             "[C]([C]([C](p[C]p[C]))[N]([C]([C]=[O])))": 1.0,
             "[C](p[C]p[C]p[C])": 1.0,
             "[C](p[C](p[C])p[C](p[C]p[N]))": 1.0,
             "[C](p[C](p[C](p[C,0][O]))p[C](p[C](p[C]p[C,0])p[N](p[C])))": 1.0,
             "[C](p[C]([C]([C])p[C](p[C]p[C,0]))p[N](p[C,0](p[C])))": 1.0,
             "[N](p[C](p[C])p[C](p[C]p[C]))": 1.0
     }
}
```
