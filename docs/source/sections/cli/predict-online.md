
(predict-online)=

# `predict-online`

The `predict-online` program performs predictions on individual SMILES and/or files of molecules, just as the {ref}`predict` program. The difference is that no models need to be trained beforehand, but instead the predictions are done in an online fashion, "training" models on the fly. Note that this is only valid for TCP models. The parameters and usage is a mix of the {ref}`train` and {ref}`predict` programs, and we thus refer the user to these pages for further information. 


## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar predict-online
```
