---
substitutions:
  br: |-
    ```{raw} html
    <br />
    ```
---

(ml_alg)=

# Scoring Algorithms

CPSign currently leverages the two machine learning libraries [LibLinear] and [LibSvm], and all their implemented algorithms. These can be used by the nonconformity measures of Conformal Predictors or Venn-ABERS to produce the final {ref}`Predictors <predictors>`. On this page you can find more details about all the currently implemented scoring algorithms.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Overview

All machine learning methods implements the interface {code}`MLAlgorithm` and one or more of the interfaces located in the package {code}`com.arosbio.modeling.ml.algorithms`. All currently available classes are registered as services and can be retrieved using Java ServiceLoader functionality, e.g., {code}`ServiceLoader.load(MLAlgorithm.class);`. All available parameters for each individual algorithm can be set using setters on each class, but can also (for the convenience of gridsearch and the CLI) be retrieved using the {code}`Configurable` interface, using the {code}`[get|set]ConfigParameters(..)` methods. The {code}`ConfigParameter` objects can tell you, e.g., which parameters that will be included in a parameter tuning by default when {code}`GridSearch#search(..)` is called without an explicit parameter grid. Furthermore, the parameters of each algorithm is set to appropriate defaults for cheminformatics in conjunction with the Signatures Molecular Descriptor (where earlier studies has produced such defaults), or the defaults used in their respective implementations in remaining cases. For further details about each algorithm, we refer to the respective websites of [LibLinear] and [LibSvm]. The Following two tables outline the algorithms and their implemented interfaces.

(regressors-tbl)=

```{eval-rst}
.. table:: **Regression algorithms**
   :align: center

   +--------------------+-----+
   |                    | SVR |
   +====================+=====+
   | LinearSVR          |  *  |
   +--------------------+-----+
   | EpsilonSVR / NuSVR |  *  |
   +--------------------+-----+


```

(classifiers-tbl)=

```{eval-rst}
.. table:: **Classification algorithms**
   :align: center

   +--------------------+----------------------+------------------+-------------------+-------------------------------+
   |                    | MultiLabelClassifier | BinaryClassifier | ScoringClassifier | PseudoProbabilisticClassifier |
   +====================+======================+==================+===================+===============================+
   | LinearSVC          | *                    | *                |         *         |                               |
   +--------------------+----------------------+------------------+-------------------+-------------------------------+
   | C_SVC / NuSVC      | *                    | *                |         *         |                               |
   +--------------------+----------------------+------------------+-------------------+-------------------------------+
   | | PlattScaledC_SVC | *                    | *                |         *         |               *               |
   | | PlattScaledNuSVC |                      |                  |                   |                               |
   +--------------------+----------------------+------------------+-------------------+-------------------------------+
   | LogisticRegression | *                    | *                |         *         |               *               |
   +--------------------+----------------------+------------------+-------------------+-------------------------------+
```

## Regression algorithms

### LinearSVR

Support Vector Regression (SVR) implemented in [LibLinear]. Restricted to a linear kernel and optimized for fast training and predictions for linear kernel SVM. When having very large data sets this algorithm is prefered as runtime is much quicker than the other regression algorithms.

### EpsilonSVR / NuSVR

Support Vector Regression (SVR) implemented in [LibSvm]. The difference between these two algorithms is that the standard {code}`cost/C` parameter (used in EpsilonSVR) is re-parameterized into {code}`nu` (used in NuSVR). The {code}`nu` parameter should be in the range \[0..1\]. Supports the following kernels: (0) LINEAR, (1) POLY, (2) RBF, (3) SIGMOID.

## Classification algorithms

### LinearSVC

Support Vector Classification (SVC) implemented in [LibLinear]. Restricted to a linear kernel and optimized for fast training and predictions for linear kernel SVM. Typically the fastest algorithm to train, and thus most appropriate when dealing with large data sets.

### C_SVC / NuSVC

Support Vector Classification (SVC) implemented in [LibSvm]. The difference between these two algorithms is that the standard {code}`cost/C` parameter (used in C_SVC) is re-parameterized into {code}`nu` (used in NuSVC). The {code}`nu` parameter should be in the range \[0..1\]. Supports the following kernels: (0) LINEAR, (1) POLY, (2) RBF, (3) SIGMOID.

### PlattScaledC_SVC / PlattScaledNuSVC

These two algorithms are based on the {code}`C_SVC` and {code}`NuSVC` algorithms, but performs an internal 5-fold data set split and uses [Platt Scaling](https://en.wikipedia.org/wiki/Platt_scaling) to output propabilities for each class (thus fitting 5 SVC models). These are generally slower to train, but needed in cases when using a nonconformity measure that requires probabilities as input.

[liblinear]: https://www.csie.ntu.edu.tw/~cjlin/liblinear/
[libsvm]: https://github.com/jeffheaton/libsvm-java
