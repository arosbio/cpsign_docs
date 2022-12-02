
(validate)=

# `validate`

The `validate` program performs a validation off a {ref}`trained model<train>`. The predictor can thus be evaluated to
see how good it performs on unknown data.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-uber.jar validate
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
