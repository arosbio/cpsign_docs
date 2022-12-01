
(predict)=

# Predict

The predict program performs predictions on individual molecules and/or files of molecules. The predictions
are made using already {ref}`trained <train>` predictor models. For TCP, predictions can also be done
'on the fly' using the {ref}`online-predict <online-predict>` program (where no training is required before hand).

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage menu can be retrieved by running command:

```text
> java -jar cpsign-[version].jar predict



                                         predict
SYNOPSIS
------------------------------------------------------------------------------------------
  predict [options]
  predict @/tmp/runconfigs/parameters.txt [options]
  predict @C:\Users\User\runconfigs\parameters.txt [options]


DESCRIPTION
------------------------------------------------------------------------------------------
  Predict new examples given trained models (derived from the 'train' program). For TCP
  there is also the 'online-predict' program that can train models on the fly, either from
  precomputed data or chem-files.


OPTIONS
------------------------------------------------------------------------------------------
  Input:
  * -mi | --model-in                         [URI | path]
       Trained CPSign model
    -sm | --smiles                           [SMILES]
       SMILES string to predict, can optionally include a blank space and a molecule
       name/identfier
    -p  | --predict-file                     [format] [opt args] [URI | path]
       File to predict. Accepted formats are SMILES, SDF or JSON

  Prediction:
    -co | --confidences                      [confidence confidence .. ]
       Confidences for predictions (e.g. '0.5,0.7,0.9' or '0.5 0.7 0.9'). Should be in the
       range [0,1]
    -di | --distances                        [distance distance .. ]
       (ACP regression only) Distances from to predicted midpoint (e.g. '0.5,2,5' or '0.5
       2 5')
    -cg | --calculate-gradient
       Calculate the Significant Signature of molecules

  Output:
    -of | --output-format                    [id | text]
       Output format of predictions, options:
         (1) json | default
         (2) TSV | smiles
         (3) sdf | sdf-v2000
         (4) sdf-v3000
         (5) CSV
       Default: 1
    -o  | --output                           [path]
       File to write output to (default is printing to screen)
    --output-inchi
       Generate InChI and InChIKey in the output
    --compress
       If the outputfile should be compressed (only possible when writing to file)

  Encryption:

  Gradient image output:
    -gi | --gradient-images
       Create a Gradient image for each predicted molecule.
    -if | --image-file                       [path]
       Path to where generated images should be saved, can either be a path to a specific
       folder or a full path including a file name (only .png file ending supported).
       Every image will be named '[name]-[count].png' or '[name]-[$cdk:title].png' where
       name is either a default name or the specified name to this parameter (e.g. '.' -
       current folder using default file name, '/tmp/imgs/DefaultImageName.png' - use
       /tmp/imgs/ as directory and use 'DefaultImageName' as file name)
       Default: imgs/GradientDepiction.png
    -cs | --color-scheme                     [text]
       The specified color-scheme (case in-sensitive), options:
         (1) blue:red
         (2) red:blue
         (3) red:blue:red
         (4) cyan:magenta
         (5) rainbow
             custom - contact Aros Bio for custom requirements!
       Default: 1
    --color-legend
       Add a color legend at the bottom of the image
    --atom-numbers
       Depict atom numbers
    --atom-number-color                      [color name] | [hex color]
       Color of the atom numbers
       Default: BLUE
    -ih | --image-height                     [text]
       The height of the generated images (in pixels)
       Default: 400
    -iw | --image-width                      [integer]
       The width of the generated images (in pixels)
       Default: 400

  Significant Signature image output:
    -si | --signature-images
       Create a Significant Signature image for each predicted molecule
    -sf | --signature-image-file             [path]
       Path to where generated images should be saved, can either be a path to a specific
       folder or a full path including a file name (only .png file ending supported).
       Every image will be named '[name]-[count].png' or '[name]-[$cdk:title].png' where
       name is either a default name or the specified name to this parameter (e.g. '.' -
       current folder using default file name, '/tmp/imgs/DefaultImageName.png' - use
       /tmp/imgs/ as directory and use 'DefaultImageName' as file name)
       Default: imgs/SigificantSignatureDepiction.png
    -hc | --highlight-color                  [color name] | [hex color]
       The color that should be used for the highlighting of the significant signature
       Default: BLUE
    --signature-color-legend
       Add a color legend at the bottom of the image
    --signature-atom-numbers
       Depict atom numbers
    --signature-atom-number-color            [color name] | [hex color]
       Color of the atom numbers
       Default: BLUE
    -sh | --signature-image-height           [text]
       The height of the generated images (in pixels)
       Default: 400
    -sw | --signature-image-width            [integer]
       The width of the generated images (in pixels)
       Default: 400

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

The full list of parameters are a bit overwhelming, so each section can be retrieved individually by
for instance:

```text
> java -jar cpsign-[version].jar predict output -h

                                         predict
------------------------------------------------------------------------------------------
  Output:
    -of | --output-format                    [id | text]
       Output format of predictions, options:
         (1) json | default
         (2) TSV | smiles
         (3) sdf | sdf-v2000
         (4) sdf-v3000
         (5) CSV
       Default: 1
    -o  | --output                           [path]
       File to write output to (default is printing to screen)
    --output-inchi
       Generate InChI and InChIKey in the output
    --compress
       If the outputfile should be compressed (only possible when writing to file)
```

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
