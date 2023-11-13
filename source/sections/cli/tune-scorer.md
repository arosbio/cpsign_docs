
(tune-scorer)=

# `tune-scorer`

The `tune-scorer` program is used for hyper-parameter optimization of the Support Vector Machine parameters *C* and *gamma*, or other hyper-parameters in case a different scorer algorithm is used. The standard options used in CPSign are normally good when using the signatures descriptor in combination with SVM, but here you can optionally run tuning of the hyper-parameters which can lead to improved performance.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar tune-scorer
```


## Finding available hyper-parameters

The available hyper-parameters to tune-scorer depends on what scorer algorithm is being used, and these are thus not of a fixed size. To see the available parameters run:

```bash
> ./cpsign-[version]-fatjar.jar explain tune-parameters
```

And find the ones that are applicable to the ML model you will use. 

## Picking parameter search space

See section {ref}`pick-grid` to see how to specify the hyper-parameter grid to evaluate. 