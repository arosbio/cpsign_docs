
#####################
Key features overview
#####################

* The Signatures molecular descriptor :ref:`[1-3] <refs>` for computing descriptors from chemistry.
* Transductive Conformal Prediction (TCP) for multi-label classification using LibLinear and LibSVM.
* Inductive Conformal Prediction (ICP) for multi-label lassification and regression.
* Aggregated Inductive Conformal Prediction (ACP) according to :ref:`[4] <refs>` for multi-label classification and regression.
* Cross-conformal Prediction (CCP) according to :ref:`[8] <refs>` for multi-label classification and regression.
* Standard p-values, smoothed p-values, linear and splines interpolation of p-values. 
* Cross Venn-ABERS Prediction (CVAP) according to :ref:`[9] <refs>` for probabilistic (binary) classification.
* Significant Signature calculation and feature highlighting according to the Gradient method described in :ref:`[5] <refs>`.
* Grid Search and cross-validation for parameter tuning, otherwise using default values according to :ref:`[6] <refs>`.
* `LibLinear <https://cdk.github.io/>`_ and `LibSVM <https://github.com/jeffheaton/libsvm-java>`_ for training machine learning models used in CP/Venn-ABERS.
