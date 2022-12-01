(aggregate)=

# Aggregate

The `aggregate` program joins data from multiple model or precomputed data set files. The models can either be {ref}`precomputed data <precompute>` or {ref}`trained<train>` ACP or CVAP models. Aggregate can be used for a high level way of running training in parallel, by first precomputing the descriptors and creating a numerical dataset. Then {ref}`train <train>` can be run in parallel for however many ICPs that you wish to use in the final Aggregated Conformal Predictor or Venn-ABERS predictor. Once all individual splits have been trained, `aggragate` can combine the model splits into the final aggregated model.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage help can be retrieved by running command:

```bash
> ./cpsign-[version]-uber.jar aggregate
```


## Example usage

```bash
> java -jar cpsign-[version].jar aggregate \
   --license /path/to/Standard-license.license \
   -m \
   models/acp_model_class_1.cpsign \
   models/acp_model_class_2.cpsign \
   --model-out \
   /tmp/acp_classification_aggregated_reg.cpsign \
   --model-name \
   Aggregated classification model

Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Aggregating classification models..
 - Loaded model 1/2
 - Aggregated model 2/2

Successfully aggregated 2 trained classification models (in total 6 ICPs)

Saving aggregated model to file..
Finished model saved at:
/private/tmp/acp_classification_aggregated_reg.cpsign
```
