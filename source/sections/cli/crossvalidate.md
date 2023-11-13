
(crossvalidate)=

# `crossvalidate`

Internal testing can be performed with conformal or Venn-ABERS models. This program initially only supported *k*-fold cross-validation but now supports all type of testing strategies within CPSign (*k*-fold CV, leave-one-out CV and a single test-train split).

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar crossvalidate
```

## Example Usage

Example (ACP classification):

```bash
> ./cpsign-[version]-fatjar.jar crossvalidate \
   -pt 1 \
   -td sdf /path/to/datafile.sdf \
   -e "Ames test categorisation" \
   -l mutagen, nonmutagen \
   -cp 0.7,0.8,0.9 \ 
   -k 10


                          -= CPSign - CROSSVALIDATE =-

Validating arguments... [done]
Loading precomputed data set... 
Loaded precomputed data set with 123 records and 1930 features. Occurrences of 
each class: 'mutagen'=64, 'nonmutagen'=59.
Starting the validation using 10-fold cross-validation strategy... [done]


Overall statistics:
 - AverageC                       : 1.13
 - AverageC_SD                    : 0.14
 - Balanced Observed Fuzziness    : 0.175
 - Balanced Observed Fuzziness_SD : 0.0581
 - Observed Fuzziness             : 0.182
 - Observed Fuzziness_SD          : 0.0549
 - Unobserved Confidence          : 0.908
 - Unobserved Confidence_SD       : 0.0392
 - Unobserved Credibility         : 0.58
 - Unobserved Credibility_SD      : 0.0891
 - Balanced Accuracy              : 0.756
 - Balanced Accuracy_SD           : 0.121
 - Classifier Accuracy            : 0.741
 - Classifier Accuracy_SD         : 0.118
 - F1Score_weighted               : 0.742
 - F1Score_weighted_SD            : 0.119
 - F1Score_macro                  : 0.731
 - F1Score_macro_SD               : 0.122
 - F1Score_micro                  : 0.741
 - F1Score_micro_SD               : 0.118
 - NPV                            : 0.771
 - NPV_SD                         : 0.183
 - Precision                      : 0.721
 - Precision_SD                   : 0.179
 - ROC AUC                        : 0.826
 - ROC AUC_SD                     : 0.0967
 - Recall                         : 0.805
 - Recall_SD                      : 0.162

Calibration plot:
Confidence	Accuracy(nonmutagen)	Accuracy(nonmutagen)_SD	Accuracy	Accuracy_SD	Accuracy(mutagen)	Accuracy(mutagen)_SD	Proportion empty-label prediction sets	Proportion empty-label prediction sets_SD	Proportion multi-label prediction sets	Proportion multi-label prediction sets_SD	Proportion single-label prediction sets	Proportion single-label prediction sets_SD
0.7	0.751	0.175	0.662	0.167	0.602	0.243	0.144	0.138	0.0	0.0	0.856	0.138
0.8	0.838	0.121	0.789	0.0949	0.766	0.189	0.0154	0.0487	0.146	0.11	0.839	0.0979
0.9	0.921	0.108	0.869	0.0674	0.851	0.112	0.00769	0.0243	0.396	0.245	0.596	0.232
```

Note here that each metric output the mean out of the *k* fold as well as the standard deviation calculated across these folds. If a single test-train split is used only one statistic is given. In general we recommend to use either `TSV` or `CSV` out to make it easier to look at model calibration.


Example (AVAP classification):

```bash
> ./cpsign-[version]-fatjar.jar cv \
   -pt 5 \
   -td sdf /path/to/datafile.sdf \
   -e "Ames test categorisation" \
   -l mutagen, nonmutagen \
   -k 5


                          -= CPSign - CROSSVALIDATE =-

Validating arguments... [done]
Loading precomputed data set...
Loaded precomputed data set with 123 records and 1930 features. Occurrences of
each class: 'mutagen'=64, 'nonmutagen'=59.
Starting the validation using 5-fold cross-validation strategy... [done]

Overall statistics:
 - Classifier Accuracy    : 0.689
 - Classifier Accuracy_SD : 0.134
 - Balanced Accuracy      : 0.7
 - Balanced Accuracy_SD   : 0.141
 - Brier Score            : 0.427
 - Brier Score_SD         : 0.119
 - Log loss               : 0.62
 - Log loss_SD            : 0.138
 - F1Score_weighted       : 0.686
 - F1Score_weighted_SD    : 0.133
 - F1Score_macro          : 0.687
 - F1Score_macro_SD       : 0.133
 - F1Score_micro          : 0.689
 - F1Score_micro_SD       : 0.134
 - Precision              : 0.733
 - Precision_SD           : 0.225
 - Recall                 : 0.639
 - Recall_SD              : 0.18
 - NPV                    : 0.693
 - NPV_SD                 : 0.156
 - ROC AUC                : 0.754
 - ROC AUC_SD             : 0.177
 - Mean P0-P1 width       : 0.278
 - Mean P0-P1 width_SD    : 0.054
 - Median P0-P1 width     : 0.227
 - Median P0-P1 width_SD  : 0.0571

Calibration plot:
Expected probability	Num examples in bin	Observed frequency	Observed frequency_SD
0.15	19.0	0.29	0.415
0.25	30.0	0.336	0.237
0.35	15.0	0.481	0.0321
0.45	8.0	0.233	0.252
0.55	17.0	0.6	0.435
0.65	15.0	0.879	0.21
0.75	10.0	0.833	0.333
0.85	18.0	0.883	0.162

```

The VAP outputs a calibration curve to, that ideally should be a straight line with slope 1 and intersect 0. For this very small dataset the are too few examples to get a descent calibration curve. In case more/less points are desired on the calibration curve, set the desired points to the `--calibration-points` flag. For instance running with `--calibration-points 0.1:0.9:0.2` gave the following curve instead:

```bash
Calibration plot:
Expected probability	Num examples in bin	Observed frequency	Observed frequency_SD
0.1	19.0	0.29	0.415
0.3	45.0	0.354	0.203
0.5	24.0	0.577	0.441
0.7	25.0	0.75	0.319
0.9	18.0	0.883	0.162
```
