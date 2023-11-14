(aggregate)=

# `aggregate`

The `aggregate` program joins data from multiple models or precomputed data set files. The models can either be {ref}`precomputed data <precompute>` or {ref}`trained<train>` ACP or CVAP models. Aggregate can be used for a high level way of running training in parallel, by first {ref}`precomputing <precompute>` the descriptors and creating a numerical dataset. Then {ref}`train <train>` can be run in parallel for however many ICPs that you wish to use in the final Aggregated Conformal Predictor or Venn-ABERS predictor. Once all individual splits have been trained, `aggragate` can combine the model splits into the final aggregated model. Note that `aggregate` reads in and loads all models into memory and validate that the final aggregate model is complete and functional, whereas the {ref}`fast-aggregate` program instead simply copies all input into a single output and do minor updates of the meta data - but do no validation of the final model being complete and functional.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar aggregate
```


## Example usage

Here is a small example, assuming that you have two partially trained input models located in the directory `/output/models/` and called e.g. `split-0.jar` and `split-1.jar`. There should be 2 and 3 ICPs in these models, respectively, and generated using the same seed and all other parameters (e.g. by running `--splits 1,2,3` and `--splits 4,5` during training).  Aggregation of these models can then be done using the bellow code, using a glob pattern to match the two input files and generate the final, complete, model in `/output/models/final-model.jar`.

```bash
> ./cpsign-[version]-fatjar.jar aggregate \
	-m /output/models/split-*.jar \
	-mn "final aggregated model" \
	-mo /output/models/final-model.jar

```
The output could look something like this:

```bash

                            -= CPSign - AGGREGATE =-

Aggregating regression models...
 - Loaded model 1/2
 - Aggregated model 2/2
Successfully aggregated 2 trained regression models (in total 5 ICPs)
Saving aggregated model to file:
/output/models/final-model.jar ... [done]
```

