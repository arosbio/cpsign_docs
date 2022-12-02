
(tune)=

# `tune`

The `tune` program is used for parameter optimization of the Support Vector Machine parameters *C* and *gamma*. The standard
options used in CPSign are normally good when using the signatures descriptors in SVM problems, but here you can optionally run
tuning of the parameters.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage menu can be retrieved by running command:

```text
> java -jar cpsign-[version].jar tune


                                           tune
SYNOPSIS
------------------------------------------------------------------------------------------
  tune [options]
  tune @/tmp/runconfigs/parameters.txt [options]
  tune @C:\Users\User\runconfigs\parameters.txt [options]


DESCRIPTION
------------------------------------------------------------------------------------------
  Perform an exhaustive grid search to find optimal SVM parameter values for Cost, Gamma
  and Epsilon (both tolerance criteria and epsilon used in loss function of SVR). For
  regression problems using the log-normalized nonconformity measure, it is also possible
  to grid search for optimizing the beta-parameter of the nonconformity measure.


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

  Grid Search:
    -op | --optimization                     [id | text]
       The criterion that should be used for optimizing the parameters. Some metrics are
       confidence-dependent and are marked with an '*', the others do not depend on a
       specific confidence. Options:
       ACP Regression:
       * (1) median_pred_width  (minimize: median prediction-interval width)
       * (2) mean_pred_width    (minimize: mean prediction-interval width)
         (3) rmse               (minimize: RMSE)
       CP Classification:
       * (1) prop_single_label  (maximize: proportion single-label predictions)
       * (2) prop_multi_label   (minimize: proportion multi-label predictions)
         (3) obs_fuzz           (minimize: observed fuzziness)
       VAP Classification:
         (1) logloss            (minimize: Log loss)
         (2) brier              (minimize: Brier score)
         (3) roc_auc            (maximize: ROC AUC)
       Default: 1
    --cost-values                            [start:stop:step] | [number number ..]
       The range of cost values that should be used, either specified as a non-empty list
       or using 'start:stop:step'. The values tested will be {2^start,
       2^(start+step),..,2^stop}
       Default: -4:12:2
    --gamma-values                           [start:stop:step] | [number number ..]
       The range of gamma values that should be used, either specified as a non-empty list
       or using 'start:stop:step'. The values tested will be {2^start,
       2^(start+step),..,2^stop}
       Default: -8:-1:2
    --epsilon-values                         [start:stop:step] | [number number ..]
       The range of epsilon values that should be used, either specified as a non-empty
       list or using 'start:stop:step'. The values tested will be {2^start,
       2^(start+step),..,2^stop}
       Default: 0.001
    --epsilon-svr-values                     [start:stop:step] | [number number ..]
       (Regression) The range of epsilon values used in epsilon-SVR that should be used,
       either specified as a non-empty list or using 'start:stop:step'. The values tested
       will be {2^start, 2^(start+step),..,2^stop}
       Default: 0.1
    --beta-values                            [number number ..]
       (Regression) If log-normalized nonconformity measure is used, tune the beta value
       by giving a list of values that should be tested. Beta values must be >= 0
    --pvalue-calc-values                     [[id | text] [id | text] ..]
       Choose which p-value calculation(s) should be used (see available ones in the
       --pvalue-calc description)

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

  Cross validation parameters:
    -k  | --cv-folds                         [integer]
       Number of folds in the cross validation (min 2 folds)
       Default: 10
    -co | --confidence                       [number]
       Confidence used in the cross validation, range [0,1]
       Default: 0.8
    --tolerance                              [number]
       Allowed tolerance for validity of generated models [0,1]
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

  Output:
    -rf | --result-format                    [id | text]
       Output format, options:
         (1) json
         (2) text | plain
         (3) CSV
         (4) TSV
       Default: 2
    --num-results                            [integer]
       Number of parameter-results to print (e.g. print the top 5 best combinations)
       Default: 5
    -a  | --all
       Print all grid search results (otherwise will just print the optimal results)
    -o  | --output                           [path]
       File to write all grid search results to (default is to print to screen)

  Encryption:

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

## Picking parameter search space

Parameter search space is fully configured either by supplying specific values for each parameter, or by giving
a triplet on the form **start:stop:step**. If the triplet is given, the parameter values that finally will be tried are the set
{2^start, 2^(start+step),..,2^stop}. When searching a large parameter space, it is possible to do a coarse-grained
search by setting a larger *step* size, when the region of interest has been found,
lower the *step* size and do a fine grained search. Currently only available in ACP and VAP modes, but the parameters obtained in ACP/VAP
is transferable to the TCP case.

## Parameter tuning β

The smoothing factor, β, of the logarithmically normalized nonconformity measure introduced in {ref}`nonconf-measure`
can be optimized with the tune program. This is done slightly different than with the *C* and *gamma* values, here you can simply add
a list of β values that you wish to test (given that you have set the logarithmically normalized nonconformity measure in a regression case):

```bash
> java -jar cpsign-[version].jar tune \
  --beta-values 0.0 0.1 0.2 0.5 \
  ..
```
