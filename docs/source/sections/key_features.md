# Key features overview

- The Signatures molecular descriptor [[1-3]](references.md) for computing descriptors from chemistry.
- ECFPs of diameter 0-6 and physico-chemical descriptors computed using [CDK](https://cdk.github.io/).
- Transductive Conformal Prediction (TCP) for binary and multi-label classification.
- Inductive Conformal Prediction (ICP) for binary and multi-label classification, as well as regression.
- Aggregated Inductive Conformal Prediction (ACP) according to [[4]](references.md) for binary and multi-label classification, as well as regression.
- Cross-conformal Prediction (CCP) according to [[8]](references.md) for binary and multi-label classification, as well as regression.
- Calculation of p-values using standard approach, smoothed and linear/splines interpolation, described in [[11]](references.md) and [[12]](references.md).
- Cross Venn-ABERS Prediction (CVAP) according to [[9]](references.md) for probabilistic (binary) classification.
- Significant Signature calculation and feature highlighting according to the Gradient method described in [[5]](references.md).
- Parameter tuning using exhaustive grid search, using cross-validation or single test-train splitting, otherwise using default values according to [[6]](references.md).
- [LIBLINEAR](https://liblinear.bwaldvogel.de/) and [LIBSVM](https://github.com/jeffheaton/libsvm-java) for training machine learning models used in CP/Venn-ABERS.
