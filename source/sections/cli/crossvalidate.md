---
substitutions:
  br: |-
    ```{raw} html
    <br />
    ```
---

(crossvalidate)=

# Crossvalidate

Crossvalidatation can be performed with ACP or CVAP, in both regression and classification. It will perform a *k*-fold crossvalidation using
**k** number of folds.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage menu can be retrieved by running command:

```text
> java -jar cpsign-[version].jar crossvalidate -h

                                      crossvalidate
SYNOPSIS
------------------------------------------------------------------------------------------
  crossvalidate [options]
  crossvalidate @/tmp/runconfigs/parameters.txt [options]
  crossvalidate @C:\Users\User\runconfigs\parameters.txt [options]


DESCRIPTION
------------------------------------------------------------------------------------------
  Performs a k-fold cross validation of the given dataset. This give an estimate on how
  good predictions will be given this dataset and these settings.


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

  Cross validation:
    -k  | --cv-folds                         [integer]
       Number of folds in cross validation (min 2, max #Training examples)
       Default: 10
    -cp | --calibration-points               [number number ..]
       Calibration points used in cross validation, equals confidences in Conformal
       Prediction and observed probabilities for Venn Prediction (each value: min 0, max
       1)
       Default: 0.8
    --calibration-points-width
       (VAP only) the width around each calibration point that should be considered for
       each calibration point, default is to use 1/[number of calibration points]. Note
       that the parameter is taken as the total width, the intervals will be
       [midpoint-0.5*width, midpoint+0.5*width]

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
    -rf | --result-format                    [id | text]
       Output format, options:
         (1) json
         (2) text | plain
         (3) CSV
         (4) TSV
       Default: 2
    -o  | --output                           [path]
       File to write cross validation results to (default is printing to screen)
    --roc
       Output the ROC curve (VAP only), the ROC curve has many points and lead to verbose
       output. Default is to only print the AUC score

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

## Example Usage

Example (ACP classification):

```bash
> java -jar cpsign-[version].jar crossvalidate \
   --license /path/to/Standard-license.license \
   -pt 1 \
   -td sdf /path/to/datafile.sdf \
   -e "Ames test categorisation" \
   -l mutagen, nonmutagen \
   -k 5

Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Randomization seed used: 1531322226985

Reading train file and performing signature generation..
Successfully parsed 123 molecules. Detected labels: 'mutagen'=64, 'nonmutagen'=59.
Generated 1930 new signatures.

Starting the cross validation..
Finished

Cross validation finished with the following stats:
Classification Confidence: 0.951
Classification Credibility: 0.564
Observed Fuzziness: 0.146
Observed Fuzziness (mutagen): 0.105
Observed Fuzziness (nonmutagen): 0.19
Set confidence: 0.8
Accuracy: 0.789
Efficiency: 0.106
```

Example (ACP regression):

```bash
> java -jar cpsign-[version].jar crossvalidate \
   --license /path/to/Standard-license.license \
   -pt 2 \
   -td sdf /path/to/datafile.sdf \
   -e BIO \
   --cv-folds 5


Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Randomization seed used: 1531322540354

Reading train file and performing signature generation..
Successfully parsed 34 molecules. Generated 286 new signatures.

Starting the cross validation..
Finished

Cross validation finished with the following stats:
RMSE: 7.593
Set confidence: 0.8
Accuracy: 0.941
Efficiency: 28.883
```

Example (AVAP classification):

```bash
> java -jar cpsign-[version.jar cv \
   --license /path/to/Standard-license.license \
   -pt 5 \
   -td sdf /path/to/datafile.sdf \
   -e "Ames test categorisation" \
   -l mutagen, nonmutagen \
   -k 5


Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Randomization seed used: 1531323046186

Reading train file and performing signature generation..
Successfully parsed 123 molecules. Detected labels: 'mutagen'=64, 'nonmutagen'=59.
Generated 1930 new signatures.

Starting the cross validation..
Finished

Cross validation finished with the following stats:
Logloss: 0.497
AUC: 0.85
Median interval width: 0.09376
Mean interval width: 0.10487

Calibration curve:
Expected     Observed        Num examples
0.05 0.0     9.0
0.15 0.067   15.0
0.25 0.0     6.0
0.35 0.333   12.0
0.45 0.524   21.0
0.55 0.733   15.0
0.65 1.0     5.0
0.75 0.727   11.0
0.85 0.789   19.0
0.95 0.9     10.0
```

The VAP outputs a calibration curve, that ideally should be a straight line with slope 1 and intersect 0. For
this very small dataset the're are too few examples to get a descent calibration curve. In case
more/less points are desired on the calibration curve, set the desired points to the `--calibration-points` flag.
For instance running with `--calibration-points 0.1:0.9:0.2` gave the following curve instead:

```bash
Calibration curve:
Expected     Observed        Num examples
0.1  0.077   13.0
0.3  0.259   27.0
0.5  0.5     32.0
0.7  0.667   30.0
0.9  0.947   19.0
```
