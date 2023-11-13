
(validate)=

# `validate`

The `validate` program performs a validation off a {ref}`trained model<train>`. The predictor can thus be evaluated to see how good it performs on unseen data.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar validate
```

## Example Usage

Example (ACP Classification):

```bash
> java -jar cpsign-[version]-fatjar.jar validate \
   --validation-property "Ames test categorisation" \
   -p sdf /path/to/validatefile.sdf \
   -cp 0.7 0.8 0.9 \
   -m path/to/model.jar


                            -= CPSign - VALIDATE =-

Validating arguments... [done]
Loading model... [done]
Loaded an ACP classification predictor with 5 aggregated models. Model has been
trained from 1314 training examples. The model endpoint is 'Ames test
categorisation'. Class labels are 'nonmutagen' and 'mutagen'.
Computing predictions...
 - Processed 20/126 molecules
 - Processed 40/126 molecules
 - Processed 60/126 molecules
 - Processed 80/126 molecules
 - Processed 100/126 molecules
 - Processed 120/126 molecules

Successfully predicted 126 molecules.

In the following results, the positive class is 'nonmutagen' and negative is
'mutagen'
Note that the following metrics are computed based on 'forced predictions' -
i.e. taking the class with the highest p-value as the predicted class: Balanced
Accuracy, Classifier Accuracy, F1Score, NPV, Precision, Recall

Overall statistics:
 - AverageC                    : 1.07
 - Balanced Observed Fuzziness : 0.148
 - Observed Fuzziness          : 0.148
 - Unobserved Confidence       : 0.916
 - Unobserved Credibility      : 0.577
 - Balanced Accuracy           : 0.785
 - Classifier Accuracy         : 0.786
 - F1Score_macro               : 0.785
 - F1Score_micro               : 0.786
 - F1Score_weighted            : 0.786
 - NPV                         : 0.776
 - Precision                   : 0.797
 - ROC AUC                     : 0.871
 - Recall                      : 0.758

Calibration plot:
Confidence	Accuracy	Accuracy(mutagen)	Accuracy(nonmutagen)	Proportion empty-label prediction sets	Proportion multi-label prediction sets	Proportion single-label prediction sets
0.7	0.746	0.766	0.726	0.0952	0.0	0.905
0.8	0.833	0.844	0.823	0.0	0.0714	0.929
0.9	0.937	0.969	0.903	0.0	0.341	0.659

```


