
(gensign)=

# `gensign`

The `gensign` program is not something that is normally part a workflow, but a way to validate the resulting signature descriptors for
molecules without starting any heavy jobs.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-uber.jar gensign
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
