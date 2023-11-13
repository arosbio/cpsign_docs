
(list-features)=

# `list-features`


The `list-features` program can be used for checking which descriptors are part of a precomputed data set or a trained model. This is primarily intended for working with data transformations, e.g. using the {ref}`transform` program. Purposes can be, e.g., finding which descriptors were kept after applying feature selection or finding which indices to apply feature scaling on. As standard feature scaling techniques such as the `Standardizer` turns the sparse Signatures into dense representations it important to know which features this should be applied to, in case combinations of Signatures and other descriptors are used.

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar list-features
```

## Verbose output

When listing features in verbose format (`-v, --verbose` flag) of precomputed dataset descriptive statistics (min,mean,median,max) will be calculated for each feature as well as checking if any feature contains missing values. This can be useful for e.g. picking features that need scaling, need to impute or remove features containing missing features and for searching for problems in your data or modeling pipeline. Note that is only possible for precomputed dataset, and not for trained models (as the training data is not saved after the training).
