(data-handling)=

# Data handling

There are two categories of data that CPSign can handle, either chemical data in SDF/CSV format or raw numerical data. CPSign is mostly intended for dealing with chemical data and that line of usage is what is most user-friendly.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Raw numerical input

### The Dataset class and Dataset.SubSet - holding raw data

There are two data structures for dealing with structured data, the `Dataset` and the `Dataset.SubSet` classes. The `SubSet` class is used for holding a single dataset, whereas the `Dataset` class holds three separate `SubSet` objects. The `Dataset` class is the preferred way of handling data, and is the input to all predictor classes for, e.g., training models. The three data sets used in the `Dataset` class are the following:

> - **NORMAL** : Standard data, where all records/examples in the set is used for training scorer models and calibrating results, as well as used in for testing in GridSearch similar scenarios.
> - **MODELING_EXCLUSIVE** : Earmarks records to be exclusively used in the *proper training set* - i.e. used for training the underlying scoring models.
> - **CALIBRATION_EXCLUSIVE** : Earmarks records to be exclusively used in the *calibration set* - i.e. used for model calibration only.

### Supported file format & loading

CPSign stores/loads (sparse) structured data in LibSVM/LIBLINEAR format which is in the form:

```bash
<value> <index>:<occurrences> <index>:<occurrences> ..
<value> <index>:<occurrences> <index>:<occurrences> ..
..
```

Also note that the \<index> **must start at 1** and not 0, to conform with LIBLINEAR and LibSVM requirements.

Loading a LIBSVM formatted file can be done using the static method {code}`fromLIBSVMFormat()` that that exists in both the `Dataset` and `SubSet` classes.

## Chemical input data

CPSign supports loading chemical data in CSV and SDF file formats, see example of using CSV and SDF in the [CPSign examples](https://github.com/arosbio/cpsign-examples) in file `ParsingChemicalFiles.java`. We include the classes `com.arosbio.chem.io.in.CSVFile` and `com.arosbio.chem.io.in.SDFile` to make it easy to load data from these file formats. See [Commons CSV](https://commons.apache.org/proper/commons-csv/) for details of how to set the CSVFormat so it matches your input file.

### Loading data from any source

Apart from loading chemical data from CSV and SDF files CPSign offers a more generic way of populating a ChemDataset by the `ChemDataset.add(Iterator<IAtomContainer> mols,...)` that allows the API-user to load data from any data source, by wrapping it within an Iterator instance. 

## Data manipulation

Simple data manipulations such as shuffling and splitting can be done directly using the `Dataset` and `Dataset.SubSet` classes, e.g., `data.shuffle(seed)`. More complex manipulations such as scaling of features, feature selection and imputation of missing values are performed using `Transformer` instances, which are located in the package `com.arosbio.modeling.data.transform` and sub-packages. All implemented transformers are registered as services and can be loaded using Java [ServiceLoader](https://docs.oracle.com/javase/8/docs/api/java/util/ServiceLoader.html), using `ServiceLoader.load(Transformer.class);`. 

Here is some example code of how to do simple tasks as shuffling and splitting:

```java
// Load data in LIBSVM format
Dataset data = Dataset.fromLIBSVMFormat(InputStream);

// Merge data from another file:
// (requires that all indexing is the same of course!)
Dataset data2 = Dataset.fromLIBSVMFormat(InputStream);
data.join(data2); // deep copy 
// or 
data.joinShallow(data2); // shallow copy - changes are seen in both instances

// Shuffle the records
data.shuffle();

// Write manipulated data to file (data now has combined two data files and random shuffled the records)
// note this is done on individual Dataset.SubSet instances
data.getDataset().writeRecords(OutputStream, true); // chose to compress or not
// Or saving in encrypted format 
EncryptionSpecification spec = ... // requires an encryption spec
data.getDataset().writeRecords(OutputStream, spec);

// Clone a dataset to make a deep copy
Dataset dataClone = data.clone();

// Splitting is performed in the Dataset.SubSet level
SubSet set = data.getDataset();

// Splitting can be done with random shuffle
SubSet[] splits = set.splitRandom(0.2); // 20% in first SubSet, 80% in second one

// Or splitting can be done statically (keep ordering)
SubSet[] staticSplit = set.splitStatic(100); // Split so first 100 records in first 
SubSet[] splitSplitFraction = set.splitStatic(0.3); // 30% in first, 70% in second

// Or splitting while preserving class balance (fails for regression!)
SubSet[] stratifiedSplits = set.splitStratified(0.3)

```

### Gotchas

> - Transformers should preferably be used on `Dataset` objects or the superclass `ChemDataset`, which makes sure to keep a reference to the transformers so that the same transformations can be applied to future test-instances when needed. 
> - When not using a `ChemPredictor`(instead using the Predictor classes directly, using non-chemical input data), the transformations are *not saved* when serializing the model using `ConfAISerializer`. Furthermore, they are not applied automatically on new test-instances, see example code below of how this should be performed (manually).
> - Transformers are applied to the current data set, but does not cause any changes in the `Descriptor` objects when applied. However, when using the `ChemPredictor` classes, the transformations that was applied to the data set prior to training will be applied at prediction-time for new instances and saving/loading of the transformations is done automatically. However, only transformers that return `true` for `Transformer.appliesToNewObjects()` will be saved in the model file.
> - Users are completely free to implement their on transformations, and by adding them to the class-path and registering them as java services, they will be saved and loaded in the same way CPSign loads the "native" transformations. These are then also usable through the CLI of CPSign.

```java
// Given a data set
Dataset data = Dataset.fromLIBSVMFormat(InputStream);

// Apply transformations
data.apply(new RobustScaler());

// Train the predictor
predictor.train(data);

// Save the predictor
File modelFile = ...
ConfAISerializer.saveModel(predictor, info, modelFile, ..);
// The above code saves the predictor and meta-data about it, but *no transformations*
// Manually save the transformations that are cached in the data instance
try(
             FileOutputStream fos = new FileOutputStream(jar);
             BufferedOutputStream buffered = new BufferedOutputStream(fos);
             JarOutputStream jarStream = new JarOutputStream(buffered, mf);
             DataSink jarSink = new JarDataSink(jarStream);
             ){
   data.saveTransformersToSink(jarSink, "");
}

// Predicting new instances that are not part of "data"
// requires manual work, first load the non-transformed features
FeatureVector test = ...;
// Apply the same transformations (cached in "data")
test = data.transform(test);
// Now 'test' can be predicted correctly

```

The code above will save the both the model itself and the transformation (`RobustScaler`) in the JAR-file. At prediction-time you will have to call one of the corresponding `loadTransformersFromSource(..)` methods from a Dataset instance and apply them to test-instances using `data.transform(FeatureVector)` for each test-instance, before calling one of the prediction-methods of the predictor itself.
