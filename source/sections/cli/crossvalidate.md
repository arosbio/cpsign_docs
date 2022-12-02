
(crossvalidate)=

# `crossvalidate`

Crossvalidatation can be performed with ACP or CVAP, in both regression and classification. It will perform a *k*-fold crossvalidation using
**k** number of folds.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-uber.jar crossvalidate
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
