(chem-predictors)=

# Chem Predictors

A `ChemPredictor` is a wrapper class that is put on top of a [`Predictor`](sparse-predictors), adding useful functionality for directly dealing with chemical data for both training and prediction tasks.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Instantiation

There is one wrapper class for each [`Predictor`](sparse-predictors) type:


|Predictor class | Wrapper class | Description |
|-|-|-|
|CPClassifier|ChemCPClassifier|ACP/CCP/TCP classification models|
|CPRegressor|ChemCPRegressor|ACP/CCP regressor models|
|AVAPClassifier|ChemVAPClassifier|Venn-ABERS type models|


## Loading data & Predict

Molecular data can be loaded directly using the `ChemDataset` class, which performs descriptor calculation on the fly, or using the `ChemPredictor` instance which then stores some meta data. The `ChemPredictor` wrapper classes outlined in the last section holds a `ChemDataset` instance so that data and the predictor is kept jointly. Loading data is most easily achieved using the convenience classes `SDFile`, `CSVFile` and `JSONFile` that allows you to get an iterator of molecules. Note that you can load data from multiple files, simply by calling the `add(Iterator)` several times, potentially using different file formats.

```java
ChemCPClassifier clf = new ChemCPClassifier(acp);

// Read from SDF v2000/v3000
Iterator<IAtomContainer> data = new SDFile(file).getIterator();
// Define the property in the SDF to read labels from
String property = ...
// Map textual labels to numerical values
NamedLabels labels = new NamedLabels("active","inactive");

// Load using wrapper method in ChemCPClassifier class
clf.addRecords(data,
			property,
			labels);
// Load using the ChemDataset directly
clf.getDataset().add(data,
            property,
            labels);

```

Loading data in this way automatically loads it in the "normal" dataset, whereas adding the `RecordType.CALIBRATION_EXCLUSIVE` or `RecordType.MODELING_EXCLUSIVE` as a forth argument to the `add()` or `addRecords()` methods will mark the data to either be used exclusively for calibration of the predictions or training of the underlying scoring model.

## Saving and loading predictor models

Both the precomputed data and the finished trained predictor model can be of interest to save. The precomputed data can be saved in case it is desired to train different predictors, possibly using different scoring implementations or parameters. The trained predictor model can be used for later predictions, deployed as a micro service etc. Serializing (saving) either data or prediction models are done using the `ModelSerializer` class - by calling one of the `saveModel` or `saveDataset` methods. Loading them back into memory is done using the same class, using one of the `loadDataset` or `loadChemPredictor` methods.

## Image generation

To get visual results from the predictions (i.e. of the significant signature), please refer to the [Image rendering](../depiction/images.md) page.
