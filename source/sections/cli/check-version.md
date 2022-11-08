---
substitutions:
  br: |-
    ```{raw} html
    <br />
    ```
---

(checkversion)=

# Check-version

The check-version program reads a CPSign model and prints information about it, i.e. what version of CPSign was used to build the model and should thus be able to use it.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage menu can be retrieved by running command:

```text
> java -jar cpsign-[version].jar check-version -h

                                      check-version
SYNOPSIS
------------------------------------------------------------------------------------------
  check-version [options]
  check-version @/tmp/runconfigs/parameters.txt [options]
  check-version @C:\Users\User\runconfigs\parameters.txt [options]


DESCRIPTION
------------------------------------------------------------------------------------------
  Get information about a CPSign model.


OPTIONS
------------------------------------------------------------------------------------------
  Input:
  * -mi | --model-in                         [URI | path]
       CPSign model

  Output:
    -rf | --result-format                    [id | text]
       Output format, options:
         (1) json
         (2) text | plain
       Default: 2
    --full
       Get full information about the given model, not only what CPSign version was used
       for building the model

  General:
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

------------------------------------------------------------------------------------------
```

No license is required for this command.

## Example Usage

```bash
> java -jar cpsign-[version].jar check-version \
   --model-in /path/to/acp_model.jar \
   --full

Model Info:
-----------
CPSign build version : 0.7.7
Model name           : sdagas
Model version        : 1.0.0_2018-11-12_15:09:05.347
Model endpoint       : Ames test categorisation
Build time           : Mon Nov 12 15:09:06 CET 2018
Predictor type       : Signatures ACP Classification predictor
Observations used    : 123
Seed used            : 4885

> java -jar cpsign-[version].jar check-version \
   --model-in /path/to/acp_model.jar \
   --full \
   -rf json

{
   "CPSign build version": "0.7.7",
   "Model name": "sdagas",
   "Model version": "1.0.0_2018-11-12_15:09:05.347",
   "Model endpoint": "Ames test categorisation",
   "Build time": "Mon Nov 12 15:09:06 CET 2018",
   "Predictor type": "Signatures ACP Classification predictor",
   "Observations used": 123,
   "Seed used": 4885
}
```
