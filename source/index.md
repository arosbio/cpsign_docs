# CPSign Documentation

CPSign is an Open Software tool for QSAR modeling using conformal and probabilistic prediction. Several descriptors can be computed, such as the Signatures molecular descriptor, ECFPs and physico-chemical using the [CDK](https://cdk.github.io/) library, and it includes standard data preprocessing transformations for tasks such as feature scaling and data filtration. It comes with [LibLinear](https://liblinear.bwaldvogel.de/) and [LibSVM](https://github.com/jeffheaton/libsvm-java) for training underlying models, but is extendable. It is completely written for the Java Virtual Machine (JVM) and can be run on all operating systems. Are you interested in seeing the source code or contribute to the project, head over to [CPSign's GitHub page](https://github.com/arosbio/cpsign).

## Table of Contents

```{toctree}
:maxdepth: 2

sections/key_features
sections/ConformalPrediction
sections/requirements
sections/license
sections/cli/CLI
sections/api/API
sections/depiction/images
sections/input
sections/customization
sections/securing_your_data
sections/references
```

% sections/cli/precompute

% sections/cli/train

% sections/cli/predict

% sections/cli/tune

% sections/cli/crossvalidate

% sections/cli/gensign
