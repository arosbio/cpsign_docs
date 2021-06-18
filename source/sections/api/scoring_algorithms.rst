.. _ml_alg: 

.. |br| raw:: html

   <br />

#################################
Scoring Algorithms
#################################

CPSign currently leverages the two machine learning libraries LibLinear_ and LibSvm_, and all their implemented algorithms. These can be used by the nonconformity measures of Conformal Predictors or Venn-ABERS to produce the final :ref:`Predictors <predictors>`. On this page you can find more details about all the currently implemented scoring algorithms.

.. _LibLinear: https://www.csie.ntu.edu.tw/~cjlin/liblinear/
.. _LibSvm: https://github.com/jeffheaton/libsvm-java

.. contents:: Table of Contents
   :depth: 3
   :backlinks: top


Overview
=========================
All machine learning methods implements the interface :code:`MLAlgorithm` and one or more of the interfaces located in the package :code:`com.arosbio.modeling.ml.algorithms`. All currently available classes are registered as services and can be retrieved using Java ServiceLoader functionality, e.g., :code:`ServiceLoader.load(MLAlgorithm.class);`. All available parameters for each individual algorithm can be set using setters on each class, but can also (for the convenience of gridsearch and the CLI) be retrieved using the :code:`Configurable` interface, using the :code:`[get|set]ConfigParameters` methods. The :code:`ConfigParameter` objects can tell you, e.g., which parameters that will be included in a parameter tuning by default when :code:`GridSearch#search(..)` is called without an explicit parameter grid. Furthermore, the parameters of each algorithm is set to appropriate defaults for cheminformatics in conjunction with the Signatures Molecular Descriptor (where earlier studies has produced such defaults), or the defaults used in their respective implementations in remaining cases. For further details about each algorithm, we refer to their respective websites LibLinear_ and LibSvm_.


Regression algorithms
=========================

LinearSVR
-----------
Support Vector Regression (SVR) implemented in LibLinear_. Restricted to a linear kernel and optimized for fast training and predictions for the linear case (should be prefered over LIBSVM implementation with a linear kernel)



EpsilonSVR
------------
Support Vector Regression (SVR) implemented in LIBSVM

NuSVR
-------
Support Vector Regression (SVR) implemented in LIBSVM. Uses :code:`nu` parameter instead of :code:`C`.



Classification algorithms
=========================

Linear SVC
------------



SVC
------------







MLAlgorithm wrapping
--------------------

CPSign wraps LibLinear and LibSvm java implementation in the classes :code:`LibLinear` and :code:`LibSvm` 
to provide a generic interface to the user. Instantiation can be done either directly by the constructors
of the classes or by the :code:`CPSignFactory`: using:

.. code-block:: java
   
   // Direct instantiation 
   LibLinear libLin = new LibLinear(new Parameter(SolverType.L1R_L2LOSS_SVC, c, eps));
   
   // Using default parameters in CPSign
   CPSignFactory factory = ...; // Instantiate 
   LibLinear libLin = factory.createLibLinearClassification(); // classification
   LibLinear libLin = factory.createLibLinearRegression(); // regression
   
   // Direct instantiation of LibSvm
   LibSvm svm = new LibSvm(new svm_parameter()); // set your parameters on the svm_parameter object
   
   // Using factory
   factory.createLibSvmClassification();
   factory.createLibSvmRegression();

Setting custom parameters
-------------------------

By default CPSign uses parameters for LibLinear and LibSVM that has been found to produce good results together with
signatures descriptors :ref:`[6] <refs>`. However, it is possible to to set all LibLinear and LibSVM parameters
programmatically through the API. This is mostly important when performing predictions where data come from
other sources than derived from signatures. Setting parameters are as straightforward as: 

.. code-block:: java
   
   // For LibLinear 
   // Create a LibLinear object either with classification or regression settings
   LibLinear liblin = factory.createLibLinearClassification();
   LibLinearParameters params = liblin.getParameters();
   
   // Either set parameters one by one
   params.setC(newC);
   params.setEpsilon(newEpsilon);
   
   // Or create new parameters from scratch
   Parameter liblinParams = new Parameter(SolverType.L1R_LR, 100, 0.5);
   liblin.setParameters(liblinParams);
   
   // For LibSvm
   // Create a LibSvm object either for classification or regression
   LibSvm impl = factory.createLibSvmClassification();
   LibSvmParameters params = impl.getParameters();
   
   // Or create new parameters from scratch
   svm_parameter svmParams = new svm_parameter();
   svmParams.C = 100;
   svmParams.eps = 0.5;
   liblin.setParameters(svmParams);
   