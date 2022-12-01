# Key features overview

- The Signatures molecular descriptor [[1-3]](refs) for computing descriptors from chemistry.
- ECFPs of diameter 0-6 and physico-chemical descriptors computed using [CDK](https://cdk.github.io/).
- Transductive Conformal Prediction (TCP) for multi-label classification.
- Inductive Conformal Prediction (ICP) for multi-label classification and regression.
- Aggregated Inductive Conformal Prediction (ACP) according to [[4]](refs) for multi-label classification and regression.
- Cross-conformal Prediction (CCP) according to [[8]](refs) for multi-label classification and regression.
- Calculation of p-values using standard approach, smoothed and linear/splines interpolation, described in [[11]](refs) and [[12]](refs).
- Cross Venn-ABERS Prediction (CVAP) according to [[9]](refs) for probabilistic (binary) classification.
- Significant Signature calculation and feature highlighting according to the Gradient method described in [[5]](refs).
- Parameter tuning using exhaustive grid search, using cross-validation or single test-train splitting, otherwise using default values according to [[6]](refs).
- [LibLinear](https://liblinear.bwaldvogel.de/) and [LibSVM](https://github.com/jeffheaton/libsvm-java) for training machine learning models used in CP/Venn-ABERS.
