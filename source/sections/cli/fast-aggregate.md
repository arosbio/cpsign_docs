
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
> ./cpsign-[version]-fatjar.jar fast-aggregate
```

## Example usage

```bash
> java -jar cpsign-[version]-fatjar.jar fast-aggregate \
   -m \
   models/acp_model_reg_*.jar \
   --model-out \
   /tmp/aggregated_reg.jar \

                         -= CPSign - FAST-AGGREGATE =-

Validating arguments... [done]
Starting to aggregate models...
Successfully aggregated 5 models.
Aggregated model saved at:
/tmp/aggregated_reg.jar

```

Note that in the above we used a glob pattern to pick all models named `acp_model_reg_*.jar` in order to match a pattern of several files using the wild card character `'*'`. 