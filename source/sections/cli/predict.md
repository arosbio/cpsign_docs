
(predict)=

# `predict`

The `predict` program takes a trained predictor model from {ref}`train` in order to predict individual molecules in SMILES format and/or files with molecular data. For TCP, predictions can also be done "on the fly" using the {ref}`predict-online` program (where no training is required beforehand).

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar predict
```

### Input parameters
The `-m, --model` parameter a is required parameter, and then either the `--smiles` and/or the `--predict-file` must be given. Then there are a few parameters that are required in some cases but optional in others, e.g. the `--confidences` does not have to be given for Venn-ABERS or conformal classifier models, where the former doesn't really on a user-set confidence level and the latter will simply output the raw p-values (but no prediction sets). 

For regression models either the `--confidences` and/or `--prediction-widths` must be given as the algorithm demands a fixed confidence to produce the prediction intervals. The `--prediction-widths` is sort of opposite of the confidence level input, you specify the width of the prediction interval and wish to know how much confidence the predictor would assign to that width of prediction interval.

### Output settings
The output can be tweaked in several ways, by default CPSign will print all predictions to the terminal - but can write them to a file using the `-o, --output` parameter. The format can be set using the `-of, --output-format` parameter, allowing the user to get json, sdf or delimited output. Note that you may run in silent mode (`-q, --quite, --silent`) in order to silence all other output from CPSign - only the predictions will then be printed to the terminal. An exception to this is in case the `--progress-bar` flag is given at the same time, which will print the progress information to the system error stream.

You may also use the `--output-inchi` flag, which will generate and add the InChI key and InChI for each input compound to the generated output.

### Prediction image generation
There are quite a few parameters that can be tweaked for generation of prediction images, they are all starting with either `si` for the **significant signature images** or `gi` for **gradient images** - more details about image generation can be found on the dedicated page {ref}`images-cli`.


