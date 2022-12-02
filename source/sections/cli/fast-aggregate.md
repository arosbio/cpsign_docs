
(fast-aggregate)=

# `fast-aggregate`

The `fast-aggregate` program aggregates partially trained ACP and VAP models, it does not work for precomputed data or TCP. It will merge multiple partial models into a large aggregated model, but it does no validation at all. Use it for speeding up training of larger ACP and VAP models, especially when using LibSvm which has a much longer training time for a given training set size.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-uber.jar fast-aggregate
```

## Example usage

```bash
> java -jar cpsign-[version].jar fast-aggregate \
   --license /path/to/Standard-license.license \
   -m \
   models/acp_model_reg_1.cpsign \
   models/acp_model_reg_2.cpsign \
   --model-out \
   /tmp/aggregated_reg.cpsign \

Running with Standard License registered to [Name] at [Company]. Expiry
date is [Date]

Starting to aggregate models..
Successfully aggregated 2 models.

Aggregated model saved at:
/private/tmp/aggregated_reg.cpsign
```
