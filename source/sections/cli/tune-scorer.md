
(tune-scorer)=

# `tune-scorer`

The `tune-scorer` program is used for parameter optimization of the Support Vector Machine parameters *C* and *gamma*. The standard
options used in CPSign are normally good when using the signatures descriptors in SVM problems, but here you can optionally run
tuning of the parameters.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-uber.jar tune-scorer
```

## Picking parameter search space

Parameter search space is fully configured either by supplying specific values for each parameter, or by giving
a triplet on the form **start:stop:step**. If the triplet is given, the parameter values that finally will be tried are the set
{2^start, 2^(start+step),..,2^stop}. When searching a large parameter space, it is possible to do a coarse-grained
search by setting a larger *step* size, when the region of interest has been found,
lower the *step* size and do a fine grained search. Currently only available in ACP and VAP modes, but the parameters obtained in ACP/VAP
is transferable to the TCP case.

## Parameter tuning β

The smoothing factor, β, of the logarithmically normalized nonconformity measure introduced in {ref}`nonconf-measure`
can be optimized with the tune program. This is done slightly different than with the *C* and *gamma* values, here you can simply add
a list of β values that you wish to test (given that you have set the logarithmically normalized nonconformity measure in a regression case):

```bash
> java -jar cpsign-[version].jar tune \
  --beta-values 0.0 0.1 0.2 0.5 \
  ..
```
