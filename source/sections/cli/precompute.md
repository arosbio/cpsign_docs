
(precompute)=

# Precompute

Precomputing data means parsing a dataset and computing the signatures descriptors
and generating a numeric data set. This is done to speed up future predictions, so that CPSign will not have to compute signatures for every new prediction.
If you are running ACP or VAP, it is likely that you are better off by just running the {ref}`train <train>` command directly to train your models (computation
of signatures descriptors are done within {ref}`train <train>` program). If you on the other hand wish to distribute the training into several *splits*, it
is likely that you gain in runtime and reduce computational resources by running this program before doing the split at the training step.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage menu can be retrieved by running command:

```text
> java -jar cpsign-[version].jar precompute


                                        precompute
SYNOPSIS
------------------------------------------------------------------------------------------
  precompute [options]
  precompute @/tmp/runconfigs/parameters.txt [options]
  precompute @C:\Users\User\runconfigs\parameters.txt [options]


DESCRIPTION
------------------------------------------------------------------------------------------
  The precompute program computes signature descriptors for chemical files, producing a
  numerical data file and a signatures descriptor file. The precompted data can later be
  used as input to other programs like train or tcp-predict, greatly reducing overall
  runtime in case several programs are used. For instance if TCP is used and different
  predictor or modeling parameters are performed using the same data, the precomputed data
  can be re-used several times instead of being recomputed for every prediction


OPTIONS
------------------------------------------------------------------------------------------
  Input:
    -mt | --model-type                       [id | text]
       Modeling type:
         (1) classification
         (2) regression
       Default: classification
    -mi | --model-in                         [URI | path]
       Model file with precomputed data (old signatures will be used as starting point,
       records will be ignored)
    -td | --train-data                       [format] [opt args] [URI | path]
       File with molecules in CSV, SDF or JSON format. run
    -e  | --endpoint                         [text]
       Endpoint property that should be used for modeling (the endoint of the model)
    -l  | --labels                           [label label]
       Label(s) for endpoint values in classification mode. More info can be found running
       "explain labels"

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

  Data manipulation:
    --duplicates                             [id | text]
       Resolve/remove potential duplicates which can make it difficult for the SVM to find
       a good decision plane. Replace duplicates by a single record with a new label or
       remove all conflicting records. Regression options:
         (1) median
         (2) mean
         (3) min
         (4) max
         (5) remove:[maximum allowed difference]
       Classification options:
         (5) remove
         (6) vote
         (7) keep:[label]
    --filters                                [id | text]
       Filters to apply on the records, currently only filters records based on the
       endpoint value for regression. Options:
         (1) min:[min]
         (2) max:[max]
         (3) range:[min]:[max]

  Output:
    -mo | --model-out                        [path]
       Model file to generate (--model-out or --model-out-dir are required to pass)
    --model-out-dir                          [path]
       Specify a directory where the model should be saved, leave naming to cpsign
       (--model-out or --model-out-dir are required to pass). Specify '.' if model should
       be generated in the current directory.
  * -mn | --model-name                       [text]
       The name of the model
    -mc | --model-category                   [text]
       The category of the model, will end up as model-endpoint in the model JAR
    -mv | --model-version                    [text]
       Optional model version in SemVer versioning format
       Default: 1.0.0_{date time string}

  Encryption:
    --encrypt                                [URI | path]
       Path to the license file that the model should be encrypted by (can be the same as
       passed to --license)

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

(exclusive-datasets)=

## "Exclusive" datasets

In the 0.6.0 version there is the addition of the flags `--model-data` and `--calibration-data`.
These two flags makes it possible to use data exclusively for either proper training or for calibration.
This could theoretically make it possible to use data from old assays for training, even though not wanting to
use it for calibration. These flags will parse the data in the same way as the *normal* training file.

**Note** that these parameters are not valid in TCP, as all data is always used and there is no division into
proper training and calibration sets.

## Example usage

```bash
> java -jar cpsign-[version].jar precompute \
   --license /path/to/Standard-license.license \
   --train-data sdf /path/to/datafile.sdf \
   --labels mutagen nonmutagen \
   --endpoint "Ames test categorisation" \
   --model-out /tmp/acp_classification.precomp \
   --model-name Ames_precomputed

Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Reading precompute file and performing signature generation..
Successfully parsed 123 molecules. Detected labels: 'mutagen'=64, 'nonmutagen'=59.
Generated 1930 new signatures.

Saving model to file..
Finished model saved at:
/private/tmp/acp_classification.precomp
```
