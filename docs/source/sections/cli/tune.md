
(tune)=

# `tune`

The `tune` program is used for hyper-parameter optimization of the Support Vector Machine parameters *C* and *gamma*, as well as other predictor hyper-parameters. The standard options used in CPSign are normally good when using the signatures descriptors in SVM problems, but here you can optionally run tuning of these.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar tune
```

(pick-grid)=

## Picking parameter search space

Parameter search space is fully configurable either by supplying specific values for each parameter, or by giving a range specification. See further information at {ref}`list-range`. The grid of parameters are set using the `-g, --grid` CLI parameter using one of the following syntaxes;

```bash
--grid C # Specify to include SVM Cost param, using the default set of parameter values to try
--grid C=10:100:10 # List syntax, enumerates the list: 10,20,..,100
--grid C=1,10,50,100 # Explicit list of numbers 1, 10, 50, 100
--grid C=b2:1:10:2 # List syntax, with different "base": 2^1, 2^3, 2^5, 2^7, 2^9 (note 10 is excluded)
```



### Example: tuning β

The smoothing factor, β, of the logarithmically normalized nonconformity measure introduced in {ref}`nonconf-measure` can be optimized with the tune program. This is done slightly different than with the *C* and *gamma* values, here you can simply add a list of β values that you wish to test (given that you have set the logarithmically normalized nonconformity measure in a regression case):

```bash
> java -jar cpsign-[version].jar tune \
  --grid beta=0.0,0.1,0.2,0.5 \
  ..
```
