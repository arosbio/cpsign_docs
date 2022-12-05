
(predict)=

# `predict`

The `predict` program takes a trained predictor model from {ref}`train` in order to predict individual molecules in SMILES format and/or files with molecular data. For TCP, predictions can also be done "on the fly" using the {ref}`online-predict` program (where no training is required beforehand).

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-uber.jar predict
```

## Input parameters
The `-m, --model` parameter a is required parameter, and then either the `--smiles` and/or the `--predict-file` must be given. Then we have a few parameters that are required in some cases but optional in others, e.g. the `--confidences` does not have to be given for Venn-ABERS or conformal classifier models, where the former doesn't really on a user-set confidence level and the latter will simply output the raw p-values (but no prediction sets). For regression models the `--confidences` and/or `--prediction-widths` must be given as the algorithm demands a fixed confidence in order to produce the confidence intervals for. The `--prediction-widths` is sort of the other way around, you specify the width of the confidence interval and wish to know how much confidence the predictor would assign to that width of intervals.

## Output settings



## Examples

## Prediction Options

Flag: `-cg` | `--calculate-gradient` 
Description: It is possible to calculate gradient and the significant signature of a molecule by passing this flag. It is comparatively computationally heavy and is therefor not computed by default. 

Flag: `-co` | `--confidences` 
Description: A list of confidences that should be used for prediction. Controls the prediction intervals for Conformal Prediction. 

Flag: `-di` | `--distance` 
Description: (Only applicable for ACP regression) Given a distance *d* (given by the input to `--distance`), predict the confidence of the real value (*y*) being within an interval +/-*d* from the
estimated value (*ŷ*) from the model (i.e. for *y* laying within the interval \[ *ŷ-d*, *ŷ+d* \]). 

## Output Options

Flag: `-of` | `--output-format` 
Description: The output type of the prediction. Can be json, smiles/plain or sdf (in mdl v2000 eller v3000 format), where json is the default option. 

Flag: `-o` | `--output` 
Description: If the prediction output should be printed to a file. 

Flag: `--output-inchi` 
Description: Generate InChI and InChIKey within CPSign and add it to the output

Flag: `--compress` 
Description: If the output file should gziped. Note that compression can only be performed when writing to a file. 

## Examples Usage

Example (ACP regression):

```bash
> java -jar cpsign-[version].jar predict \
   --license /path/to/Standard-license.license \
   --model-in /tmp/datamodels/chang.cpsign \
   -co 0.5, 0.7, 0.9 \
   --output-format json \
   --calculate-gradient \
   -sm "O=Cc1ccc(O)c(OC)c1"

Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Starting to load the prediction model..
Loaded an ACP regression predictor with 5 aggregated models. Model has been trained from
34 training examples. The model endpoint is 'BIO'.

Starting to do predictions..
{
     "prediction": {
             "intervals": [
                     {
                             "rangeUpperCapped": 5.765,
                             "rangeUpper": 5.765,
                             "distance": 3.92,
                             "rangeLower": -2.074,
                             "confidence": 0.7,
                             "midpoint": 1.846,
                             "rangeLowerCapped": 0.05
                     },
                     {
                             "rangeUpperCapped": 31.0,
                             "rangeUpper": 34.545,
                             "distance": 32.699,
                             "rangeLower": -30.854,
                             "confidence": 0.8,
                             "midpoint": 1.846,
                             "rangeLowerCapped": 0.05
                     },
                     {
                             "rangeUpperCapped": 31.0,
                             "rangeUpper": 34.545,
                             "distance": 32.699,
                             "rangeLower": -30.854,
                             "confidence": 0.9,
                             "midpoint": 1.846,
                             "rangeLowerCapped": 0.05
                     }
             ]
     },
     "molecule": {
             "InChI": "InChI=1S\/C8H8O3\/c1-11-8-4-6(5-9)2-3-7(8)10\/h2-5,10H,1H3",
             "SMILES": "O=Cc1ccc(O)c(OC)c1",
             "InChIKey": "MWOOGOJBHIARFG-UHFFFAOYSA-N"
     },
     "gradientResult": {
             "significantSignature": "[O](=[C])",
             "atomValues": {
                     "0": -0.267,
                     "2": 0.166,
                     "3": -0.078,
                     "4": 0.087,
                     "8": -0.099,
                     "9": -0.113,
                     "10": 0.087
             },
             "significantSignatureHeight": 1
     }
}
Successfully predicted 1 molecule
```

Output in JSON format give a JSON string for each molecule that is predicted, each containing two or three top-level keys;
**prediction**, **molecule** and **gradientResult** (only if gradient calculation is specified).
The prediction output looks different depending on the predictor used but should be straight forward to read and use in
downstream applications.

## Image rendering

Image rendering is described in detail in {ref}`Image rendering<images>`.
