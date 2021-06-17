.. _sign_predictors: 

.. |br| raw:: html

   <br />
 
Signatures Predictor
====================

A Signatures Predictor are a wrapper put on top of a :ref:`Predictor <predictors>`, adding useful functionality 
for handling predictions made for chemical compounds described with the signatures descriptor :ref:`[2]<refs>`.

.. contents:: Table of Contents
   :depth: 3
   :backlinks: top


Instantiation
-------------

There is one wrapper class for each :ref:`Predictor type<predictors>`, instantiation is done either by :code:`CPSignFactory`
or the constructors of each class:

 - CVAP : :code:`SignaturesVAPClassification` class or :code:`CPSignFactory.createSignaturesVAPClassification`
 - ACP Classification : :code:`SignaturesCPClassification` class or :code:`CPSignFactory.createSignaturesCPClassification`
 - ACP Regression : :code:`SignaturesCPRegression` class or :code:`CPSignFactory.createSignaturesCPRegression`
 - TCP Classification : :code:`SignaturesCPClassification` class or :code:`CPSignFactory.createSignaturesCPClassification`
   
Loading data & Predict
----------------------

Once instantiated, the Signatures-wrapper object offers a way for loading training data from a set of different 
file-types using the :code:`fromMolsIterator` methods. 
These methods are only accessible with the Standard or Pro licenses. There are two types of iterators that can be given to these methods,
either an ``Iterator<IAtomContainer>`` that also requires the endpoint to be given, which should then be stored as a property in the
``IAtomContainer`` or an  ``Iterator<Pair<IAtomContainer,Double>>`` that give an IAtomContainer with it's associated endpoint value directly.
The iterator can be of any origin, whether it be a database or some other file format of your preference. For convenience we have the 
classes ``SDFile``, ``CSVFile`` and ``JSONFile`` that allows you to get the iterator in an easy fashion. Note that you in this way can load data from 
multiple files, simply by calling :code:`fromChemFile` or :code:`fromMolsIterator` once for each file/data source. 
CPSign can in this way merge multiple datasources, from multiple formats.

.. code-block:: java

   // From an SDFile 
   List<String> labels = Arrays.asList("0", "1");
   String endpoint = "class";
   predictor.fromMolsIterator(new SDFile(sdf).getIterator(), endpoint, new NamedLabels(labels));
   // From CSV-file
   CSVFile csv = new CSVFile(csv);
   csv.setDelimiter(delim);
   String endpointHeaderColumn = "target";
   predictor.fromMolsIterator(csv.getIterator(), endpointHeaderColumn, new NamedLabels(labels));
   
CPSign version 0.6.0 introduced the possibility to use partitions of data exclusively for either training of models (proper training)
or for calibration. This is handled at the API level by introducing the :code:`Dataset.java` class that holds a single dataset 
and the :code:`Problem.java` class now holds three datasets; *dataset*, *calibrationExclusive* and *modelingExclusive*. These can be manipulated
directly if one would like to do so, or if the datasets are kept in separate files that is solved by calling the :code:`fromMolsIterator` methods
with an extra argument that takes the enum :code:`RecordType` as such:

.. code-block:: java

   // Use records in molsIterator for only calibration set 
   predictor.fromMolsIterator(molsIterator, RecordType.CALIBRATION_EXCLUSIVE);
   

Saving and loading predictor models
-----------------------------------

Both the precomputed data and the finished trained predictor can be of interest to save. The precomputed
data can be saved in case it is desired to train different predictors, possibly using different scoring
implementations or parameters. The trained predictor model can be used for later predictions and
be distributed to partners etc. Precomputed models can be saved through the :code:`ModelCreator` class, 
whereas the trained predictors can be saved both using the :code:`ModelCreator` class and calling the :code:`save()`
method of the Signatures wrapper class.


Image generation
----------------

To get visual results from the predictions (i.e. of the significant signature), please refer to the :ref:`Image rendering <images>` page.

