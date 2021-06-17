.. _online-predict: 
 
.. |br| raw:: html

   <br />



Online-predict
==============
The online-predict program performs predictions on individual molecules and/or files of molecules, just as the :ref:`predict <predict>` program. 
The difference is that no models need to be trained before hand, but instead the predictions are done in an online fashion, training
models on the fly.

.. contents:: Table of Contents
   :depth: 3
   :backlinks: top

Parameters
----------
The full usage menu can be retrieved by running command: 

.. code-block:: text

   > java -jar cpsign-[version].jar online-predict
   
   
   
                                         online-predict
   SYNOPSIS
   ------------------------------------------------------------------------------------------
     online-predict [options]
     online-predict @/tmp/runconfigs/parameters.txt [options]
     online-predict @C:\Users\User\runconfigs\parameters.txt [options]
   
   
   DESCRIPTION
   ------------------------------------------------------------------------------------------
     Train and predict new examples on the fly. Will not save the model. Currently only
     available for TCP.
   
   
   OPTIONS
   ------------------------------------------------------------------------------------------
     Input:
       -mi | --model-in                         [URI | path]
          Precomputed CPSign classification model
       -td | --train-data                       [URI | path]
          File with molecules in SMILES, SDF or JSON format (used for deriving the predictive
          model)
       -sm | --smiles                           [SMILES]
          SMILES string to predict, can optionally include a blank space and a molecule
          name/identfier
       -p  | --predict-file                     [format] [opt args] [URI | path]
          File to predict. Accepted formats are SMILES, SDF or JSON
       -e  | --endpoint                         [text]
          Endpoint property that should be used for modeling (the endoint of the model)
       -l  | --labels                           [label label]
          Label(s) for endpoint values in classification mode. More info can be found running
          "explain labels"
   
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
       --nonconf-measure                        [text]
          Nonconformity measure that should be used, see documentation for clarifications.
          Run "explain ncm" to get further information 
          Options (Classification):
            (11) NegativeDistanceToHyperplane
            (12) PositiveDistanceToHyperplane
            (13) ProbabilityEstimates (Only for ProbabilisticLibSVM - slower to compute)
          Default: 11
       --percentiles                            [integer]
          The maximum number of molecules used for calculating percentiles. This will only be
          used in case image-generation should performed.
          Default: 1000
   
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
   
     Prediction:
       -co | --confidences                      [confidence confidence .. ]
          Confidences for predictions (e.g. '0.5,0.7,0.9' or '0.5 0.7 0.9'). Should be in the
          range [0,1]
       -cg | --calculate-gradient
          Calculate the Significant Signature of molecules
   
     Output:
       -of | --output-format                    [id | text]
          Output format of predictions, options:
            (1) json
            (2) smiles | plain
            (3) sdf | sdf-v2000
            (4) sdf-v3000
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

   
The list of parameters are even larger than the one for :ref:`predict <predict>` as there are more input options, options for signature
generation and modeling. Once again we can retrieved parameters by section individually, using for instance: 

.. code-block:: text

   > java -jar cpsign-[version].jar online-predict input
   
                                          online-predict
   ------------------------------------------------------------------------------------------
     Input:
       -mi | --model-in                         [URI | path]
          Precomputed CPSign classification model
       -td | --train-data                       [URI | path]
          File with molecules in SMILES, SDF or JSON format (used for deriving the predictive
          model)
       -sm | --smiles                           [SMILES]
          SMILES string to predict, can optionally include a blank space and a molecule
          name/identfier
       -p  | --predict-file                     [format] [opt args] [URI | path]
          File to predict. Accepted formats are SMILES, SDF or JSON
       -e  | --endpoint                         [text]
          Endpoint property that should be used for modeling (the endoint of the model)
       -l  | --labels                           [label label] | [label,label]
          Label(s) for endpoint values in classification mode. More info can be found running
          "explain labels"




Examples Usage
--------------

TCP classification with chemical input data:

.. code-block:: bash

   > java -jar cpsign-[version].jar online-predict \
      --license /path/to/Standard-license.license \
      --smiles O=Cc1ccc(O)c(OC)c1 \
      --endpoint "Ames test categorisation" \
      --labels mutagen,nonmutagen \
      --time \
      --percentiles 0 \
      --train-data sdf data/ames_small.sdf.gz
   
   Running with Standard License registered to [Name] at [Company]. Expiry
   date is [Date]
   
   Reading train file and performing signature generation..
   Successfully parsed 123 molecules. Detected labels: 'mutagen'=64, 'nonmutagen'=59.
   Generated 1930 new signatures.
   (1 s)
   
   Training TCP predictor..
   Finished
   (0 s)
   
   Starting to do predictions..
   {
   	"prediction": {
   		"pValues": {
   			"nonmutagen": 0.204,
   			"mutagen": 0.0
   		}
   	},
   	"molecule": {
   		"SMILES": "O=Cc1ccc(O)c(OC)c1"
   	}
   }
   Successfully predicted 1 molecule
   (0 s)

Parameters are fairly consistent with a mix of parameters for :ref:`train <train>` and :ref:`predict <predict>`, 
apart for missing arguments for choosing predictor type as only TCP-classification is available. 