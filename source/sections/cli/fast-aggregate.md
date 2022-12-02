
(fast-aggregate)=

# `fast-aggregate`

The `fast-aggregate` program aggregates partially trained ACP and VAP models, it does not work for precomputed data or TCP. It will merge multiple partial models into a large aggregated model, but it does no validation at all. Use it for speeding up training of larger ACP and VAP models, especially when using LibSvm which has a much longer training time for a given training set size.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage menu can be retrieved by running command:

```text
> java -jar cpsign-[version].jar fast-aggregate

                                      fast-aggregate
SYNOPSIS
------------------------------------------------------------------------------------------
  fast-aggregate [options]
  fast-aggregate @/tmp/runconfigs/parameters.txt [options]
  fast-aggregate @C:\Users\User\runconfigs\parameters.txt [options]


DESCRIPTION
------------------------------------------------------------------------------------------
  The fast-aggregate program joins partially trained predictors into the final aggregated
  one. This program facilitate a high level way of distributing the training step for ACP
  and VAP predictors. Use the --splits flag in the 'train' program to train a partial
  predictor, and then use fast-aggregate to merge everything together.


OPTIONS
------------------------------------------------------------------------------------------
  Input:
  * -m  | --modelfiles                       [[URI | path] [URI | path] ..]
       A list (space or comma-separated) of models that should be aggregated. It is
       allowed to give directories, glob patterns (with wildcard characters), explicit
       files or URIs. Note that models can be a mix of non-encrypted and encrypted models.
    -af | --accept-fail
       Accept failure if a model cannot be added to the aggregated model (i.e. if model is
       of wrong type etc.). Default is to fail execution

  Output:
  * -mo | --model-out                        [path]
       Model file to generate

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

## Example usage

```bash
> java -jar cpsign-[version].jar fast-aggregate \
   --license /path/to/Standard-license.license \
   -m \
   models/acp_model_reg_1.cpsign \
   models/acp_model_reg_2.cpsign \
   --model-out \
   /tmp/aggregated_reg.cpsign \

Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Starting to aggregate models..
Successfully aggregated 2 models.

Aggregated model saved at:
/private/tmp/aggregated_reg.cpsign
```
