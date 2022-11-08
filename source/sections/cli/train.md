---
substitutions:
  br: |-
    ```{raw} html
    <br />
    ```
---

(train)=

# Train

Train is for training predictors that later can be used for prediction. Input to
train is either precomputed data from the {ref}`precompute <precompute>` program or compound
data directly in SDF/SMILES/JSON file format.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage menu can be retrieved by running command:

```text
> java -jar cpsign-[version].jar train


                                          train
SYNOPSIS
------------------------------------------------------------------------------------------
  train [options]
  train @/tmp/runconfigs/parameters.txt [options]
  train @C:\Users\User\runconfigs\parameters.txt [options]


DESCRIPTION
------------------------------------------------------------------------------------------
  Train an Aggragated Conformal Predictor (ACP), Venn-ABERS Predictor (VAP) or
  Transductive Conformal Predictor (TCP). The trained models can later be used in
  predictions. For ACP and VAP, the underlying scoring algorihtm is trained and can be
  reused for all further predictions, whereas the TCP predictor only precomputes the data
  and sets parameters for future training+predictions (as the underlying scoring algorithm
  needs to be trained for each new prediction). Input to train can either be precomputed
  data from 'precompute' or compound data in SMILES/SDF/JSON.


OPTIONS
------------------------------------------------------------------------------------------
  Input:
    -mi | --model-in                         [URI | path]
       Model file with precomputed data
    -td | --train-data                       [format] [opt args] [URI | path]
       File with molecules in CSV, SDF or JSON format. run
    -e  | --endpoint                         [text]
       Endpoint property that should be used for modeling (the endoint of the model)
    -l  | --labels                           [label label]
       Label(s) for endpoint values in classification mode. More info can be found running
       "explain labels"

  Predictor:
    -pt | --ptype | --predictor-type         [id | text]
       Predictor type:
         (1) ACP_Classification
         (2) ACP_Regression
         (3) TCP_Classification
         (5) VAP_Classification
       Default: 1
    -ss | --sampling-strategy                [id | text]
       Strategy used for sampling data to aggregated models (non TCP):
         (1) random
         (2) random_stratified (classification only)
         (3) folded
         (4) folded_stratified (classification only)
       Default: 1
    -nr | --nr-models                        [integer]
       (ACP/VAP) Number of models that should be aggregated
       Default: 1
    -cr | --calibration-ratio                [number]
       (ACP/VAP) Part of training set used as calibration set, range (0,1)
       Default: 0.2
    --nonconf-measure                        [text]
       Nonconformity measure that should be used, see documentation for clarifications.
       Run "explain ncm" to get further information
       Options (Regression) :
         (1)  LogNormalized
         (2)  Normalized
         (3)  AbsDiff
       Options (Classification):
         (11) NegativeDistanceToHyperplane
         (12) PositiveDistanceToHyperplane
         (13) ProbabilityEstimates (Only for ProbabilisticLibSVM - slower to compute)
       Default: 1 or 11 (regression / classification)
    --nonconf-beta                           [number]
       If log-normalized nonconformity measure is chosen, optionally set a beta value (>=
       0)
       Default: 0.0
    --pvalue-calc                            [id | text]
       Choose the calculation of p-values (and nonconformity score for regression).
       Options:
         (1) Standard
         (2) Smoothed
         (3) Linear_interpolation
         (4) Spline_interpolation
       Default: 1
    --percentiles                            [integer]
       The maximum number of molecules used for calculating percentiles. This is a very
       time consuming step in the training. Percentiles are only used for image rendering
       and calculating gradients for predictions, save time and set this flag to 0 if
       neither of this will be used.
       Default: 1000
    --percentiles-data                       [format] [opt args] [URI | path]
       File with molecules that exclusively should be used for calculating percentiles
       (used when calculating gradients and generating images). Default behaviour is to
       re-use the training file but when using precomputed data this is not an option and
       this flag must be given if percentiles should be calculated.
    --splits                                 [integer integer ..]
       (ACP/VAP) Run only a specific set of training splits. A means of parallelising the
       training step. If a folded sampling strategy is used, the random seed *must be
       static*! Indexing starts at 1, i.e. allowed indexes are [1,#num_models]

  Modeling:
    -i  | --impl                             [id | text]
       Scoring algorithm (i.e. underlying machine learning implementation):
         (1) LibLinear
         (2) LibSvm
         (3) ProbabilisticLibSvm
       Default: 1
    --cost                                   [number]
       User defined Cost value in SVM training
       Default: 50.0
    --gamma                                  [number]
       User defined Gamma value in SVM training (only used in libsvm)
       Default: 0.002
    --epsilon                                [number]
       User defined tolerance of termination criterion
       Default: 0.001
    --epsilon-svr                            [number]
       User defined epsilon in loss function of epsilon-SVR
       Default: 0.1

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

## Example Usage (ACP regression)

```bash
> java -jar cpsign-[version].jar train \
   --license /path/to/Standard-license.license \
   -td sdf /path/to/datafile.sdf \
   -e "BIO" \
   -nr 5 \
   -i liblinear \
   --model-out /tmp/Chang_BIO.cpsign \
   --model-name Chang_BIO \
   -pt 2


Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Reading train file and performing signature generation..
Successfully parsed 34 molecules. Generated 286 new signatures.

Training ACP Regression predictor with 5 models
 - Trained model 1/5
 - Trained model 2/5
 - Trained model 3/5
 - Trained model 4/5
 - Trained model 5/5

Saving model to file..
Finished model saved at:
/private/tmp/Chang_BIO.cpsign
```

## "Exclusive" datasets

For parameters `--model-data` and `--calibration-data` the same holds as for the precompute command, see {ref}`Exclusive datasets<exclusive_datasets>`.

## Important performance note

The default behavior of CPSign is to compute percentiles (see {ref}`Molecule Gradient<moleculeGradient>`) when training a model. This is in most
cases the most time consuming part of the training procedure as it requires to make a huge amount of predictions. This is
**only required when rendering images or calculating molecule gradients**, so if you do not intend to use this with the
trained models, you can gain a lot in runtime by setting the `--percentiles` flag to 0. Or you can at least lower the amount of molecules used
for calculating the gradients to less than 1000 molecules which is the default. When using LibLinear this is not likely to have as big impact
on runtime as the time for making predictions is so small.

## Nonconformity Measures

See information about the available nonconformity measures in the section {ref}`Nonconformity measures <nonconf_measure>` .
