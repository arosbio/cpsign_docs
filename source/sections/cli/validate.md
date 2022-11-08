---
substitutions:
  br: |-
    ```{raw} html
    <br />
    ```
---

(validate)=

# Validate

The validate program performs a validation off a {ref}`trained model<train>`. The predictor can thus be evaluated to
see how good it performs on unknown data.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage menu can be retrieved by running command:

```text
> java -jar cpsign-[version].jar validate



                                         validate
SYNOPSIS
------------------------------------------------------------------------------------------
  validate [options]
  validate @/tmp/runconfigs/parameters.txt [options]
  validate @C:\Users\User\runconfigs\parameters.txt [options]


DESCRIPTION
------------------------------------------------------------------------------------------
  Use a test-file with existing (true) labels to validate a Predictor. The normal
  execution will only report overall statistics, but it is possible to print all predicted
  result to json, smiles or sdf file format.


OPTIONS
------------------------------------------------------------------------------------------
  Input:
  * -mi | --model-in                         [URI | path]
       Trained CPSign model
  * -p  | --predict-file                     [format] [opt args] [URI | path]
       File to use for validation. Accepted formats are CSV, SDFfile or JSON.
    -ve | --validation-endpoint              [text]
       (SDFile) Name of field with true label, should match a property in the predict file
       (CSV) Name of the column to use for validation, should match header of that column
       (JSON) JSON-key for the property with the true response value

  Validation:
    -co | --confidences                      [confidence confidence .. ]
       (ACP/TCP) Confidences for predictions (e.g. '0.5,0.7,0.9' or '0.5 0.7 0.9'). Should
       be in the range [0,1]
       Default: 0.8

  Output:
    -rf | --result-format                    [id | text]
       Output format, options:
         (1) json
         (2) text | plain
         (3) CSV
         (4) TSV
       Default: 2
    --roc
       Output the ROC curve (VAP only), the ROC curve has many points and lead to verbose
       output. Default is to only print the AUC score
    --print-predictions
       Print the prediction output in json/csv/sdf format (default is only printing
       overall statistics)
    -of | --output-format                    [text]
       Output format of predictions (only applicable if the --print flag is given),
       options:
         (1) json
         (2) smiles | plain
         (3) sdf | sdf-v2000
         (4) sdf-v3000
       Default: 1
    -o  | --output                           [path]
       File to write prediction output to (default is printing to screen). Giving this
       parameter sets the --print flag to true
    --output-inchi
       Generate InChI and InChIKey in the output
    --compress
       If the outputfile should be compressed (only possible when writing to file)

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

## Example Usage

Example (ACP Classification):

```bash
> java -jar cpsign-[version].jar validate \
   --license /path/to/Standard-license.license \
   --validation-endpoint "Ames test categorisation" \
   -p sdf /path/to/validatefile.sdf \
   -co 0.7 0.8 0.9 \
   -mi /path/to/model.cpsign

Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Loading model..
Loaded an ACP classification predictor with 2 aggregated models. Model has been trained
from 123 training examples. The model endpoint is 'Ames test categorisation'. Class labels
are 'nonmutagen' and 'mutagen'.

Starting to perform validation..
 - Predicted 100/126 molecules
Successfully predicted 126 molecules

==========================================================================================

Validation result for confidence level set to 0.7:
 - Accuracy: 0.976
 - Single label predictions: 0.992
 - Double label predictions: 0.008
 - Mean classification confidence: 0.963
 - Mean classification credibility: 0.765


Validation result for confidence level set to 0.8:
 - Accuracy: 0.984
 - Single label predictions: 0.905
 - Double label predictions: 0.095
 - Mean classification confidence: 0.963
 - Mean classification credibility: 0.765


Validation result for confidence level set to 0.9:
 - Accuracy: 0.992
 - Single label predictions: 0.849
 - Double label predictions: 0.151
 - Mean classification confidence: 0.963
 - Mean classification credibility: 0.765
```

In this case we validated the results given the same input file as the model was trained of, so the results are
much better than expected, producing accuracies much higher than the ones asked for. In the case you are validating
the results using a non-seen validation set the accuracies should be close to desired confidence levels.
