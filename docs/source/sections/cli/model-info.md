
(checkversion)=

# `model-info`

The `model-info` program reads a CPSign model or precomputed data set and prints information about it, i.e., what version of CPSign was used to build the model and should thus be able to use it.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar model-info
```

## Example Usage

Here using the `-v | --verbose` flag to get more information than the build version of CPSign. 

```bash
> ./cpsign-[version]-fatjar.jar model-info \
   --model-in /path/to/acp_model.jar \
   -v

Model Info:
-----------
CPSign build version : 0.7.7
Model name           : sdagas
Model version        : 1.0.0_2018-11-12_15:09:05.347
Model endpoint       : Ames test categorisation
Build time           : Mon Nov 12 15:09:06 CET 2018
Predictor type       : Signatures ACP Classification predictor
Observations used    : 123
Seed used            : 4885

> ./cpsign-[version]-fatjar.jar model-info \
   --model-in /path/to/acp_model.jar \
   -v \
   -rf json

{
   "CPSign build version": "0.7.7",
   "Model name": "sdagas",
   "Model version": "1.0.0_2018-11-12_15:09:05.347",
   "Model endpoint": "Ames test categorisation",
   "Build time": "Mon Nov 12 15:09:06 CET 2018",
   "Predictor type": "Signatures ACP Classification predictor",
   "Observations used": 123,
   "Seed used": 4885
}
```
