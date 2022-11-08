(data-handling)=

# Data handling

There are two categories of data that CPSign can handle, either chemical data in SDF/CSV format or raw numerical data. CPSign is mostly intended for dealing with chemical data and that line of usage is what is most user-friendly.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Raw numerical input

### The Problem class - holding data sets

There are two data structures for dealing with data sets, either the {code}`Dataset` or the {code}`Problem` class. The Dataset class is intended for handling of a single data set, whereas the Problem class holds three separate {code}`Dataset` objects. The {code}`Problem` class is the prefered way of handling with data sets, and is the input to all predictor algorithms for, e.g., training models. The three data sets used in the Problem class are the following:

> - **NORMAL** : Standard data set, where all records/examples in the set is used for training and calibrating the predictor models.
> - **MODELING_EXCLUSIVE** : Earmarks records that, by the sampling strategy, should exclusively be put in the 'proper training set' and thus only be used for training the underlying scoring models.
> - **CALIBRATION_EXCLUSIVE** : Earmarks records that, by the sampling strategy, should exclusively be used for calibrating predictions.

### Supported file format & loading

CPSign stores/loads numerical data in LibSVM/LIBLINEAR format which is in the form:

```bash
<value> <index>:<occurrances> <index>:<occurrances> ..
<value> <index>:<occurrances> <index>:<occurrances> ..
..
```

Also note that the \<index> **must start at 1** and not 0, to conform with LIBLINEAR and LibSVM requirements.

Loading a LIBSVM formatted file can be done using the static method {code}`fromLIBSVMFormat()` that that exists in both {code}`Dataset` and {code}`Problem` classes.

## Chemical input data

CPSign supports loading data natively using CSV and SDF file formats, see example of using CSV and SDF in the [CPSign examples](https://github.com/arosbio/cpsign-examples) in file *ParsingChemicalFiles.java*. We include the classes {code}`com.arosbio.chem.io.in.CSVFile` and {code}`com.arosbio.chem.io.in.SDFile` for simplifying the loading of data from these file types. See [Commons CSV](https://commons.apache.org/proper/commons-csv/) for details of how to set the CSVFormat so it matches the input file.

### Loading data from any datasource

Apart from loading molecules from CSV and SDF files CPSign offers a more generic way of populating a ChemicalProblem of a {code}`SignaturesPredictor` by the methods {code}`fromMolsIterator(..)` that allows the API-user to wrap their loading from the data source in a Java-Iterator. In this way, CPSign will integrate to any type of datasource; whether it’s a database or some other source.

## Data manipulation

Simple data manipulations such as shuffling and splitting can be done directly using the {code}`Dataset` and {code}`Problem` classes, e.g., {code}`data.shuffle(seed)`. More complex manipulations such as scaling of features, feature selection and imputation of missing values are performed using {code}`Transformer` objects, which are located in the package {code}`com.arosbio.modeling.data.transform` and sub-packages. All implemented transformers are registered as services and can be loaded using Java [ServiceLoader](https://docs.oracle.com/javase/8/docs/api/java/util/ServiceLoader.html), using {code}`ServiceLoader.load(Transformer.class);`.

### Gotchas

> - Transformers should preferably be used on {code}`Problem` objects (or the {ref}`sign_predictors`), which makes sure to keep a reference to the transformers so that the same transformations can be applied to future test-instances when needed.
> - If using a non-SignaturesPredictor type of predictor, the transformations are *not* saved when saving the model using {code}`ModelCreator`. They are not applied automatically on new test-instances, see example code below of how this is done.
> - Transformers are applied to the current data set, but does not cause any changes in the {code}`Descriptor` objects when applied. However, when using the {code}`SignaturesPredictor` classes, the transformations that was applied to the data set prior to training will be applied at predict-time for new instances and saving/loading of the transformations is done automatically. However, only transformers that return true for {code}`Transformer.appliesToNewObjects()` will be saved in the model file.
> - Users are completely free to implement their on transformations, and by adding them to the class-path and registering them using java services, they will be saved and loaded in the same way CPSign native transformations are done. This is also possible through the CLI of CPSign.

```java
// Given a data set
Problem data = Problem.fromLIBSVMFormat(InputStream);

// Apply transformations
data.apply(new RobustScaler());

// Train the predictor
predictor.train(data);

// Save the predictor
File modelFile = ...
ModelCreator.generateTrainedModel(predictor, info, modelFile, ..);
// This saves the predictor and meta-data about it, but *no transformations*
// Save the transformations saved in the Problem
try(
             FileOutputStream fos = new FileOutputStream(jar);
             BufferedOutputStream buffered = new BufferedOutputStream(fos);
             JarOutputStream jarStream = new JarOutputStream(buffered, mf);
             DataSink jarSink = new JarDataSink(jarStream);
             ){
   data.saveTransformersToSink(jarSink, "");
}
```

Using the above code will save the available transformations in the JAR-file together with the model and keep everything together. At predict-time you will have to call one of the corresponding {code}`loadTransformersFromSource(..)` methods from a Problem instance and apply them to test-instances using {code}`transform(FeatureVector)` for each test-instance, before calling a prediction-method of the predictor itself.
