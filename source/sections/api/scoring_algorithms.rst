.. _ml_alg: 

.. |br| raw:: html

   <br />
 
Scoring Algorithms
==================

CPSign has two different machine learning algorithms implemented, LibLinear_ and LibSvm_, and these are used
as scoring algorithms for the :ref:`Predictor <predictors>` used. 

.. _LibLinear: https://www.csie.ntu.edu.tw/~cjlin/liblinear/
.. _LibSvm: https://www.csie.ntu.edu.tw/~cjlin/libsvm/

.. contents:: Table of Contents
   :depth: 3
   :backlinks: top


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
   