(custom)=

# Adding custom extensions to CPSign

As CPSign is now an Open Source project, it is fully possible to add your own improvements and making pull request on GitHub. Here we go through how you extend the code base with new functionality, whether it is a new nonconformity measure, metric or anything else.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

(conf-ai-extensions)=
## ConfAI extensions 

CPSign is relying on the [Java ServiceLoader](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/ServiceLoader.html) for discovering classes that should be instantiated. The above link should provide enough information in how to write and make your custom additions available to CPSign. The following classes can be extended and discovered as services:

```
com.arosbio.data.transform.Transformer
com.arosbio.ml.algorithms.MLAlgorithm
com.arosbio.ml.cp.nonconf.calc.PValueCalculator
com.arosbio.ml.cp.nonconf.NCM
com.arosbio.ml.sampling.SamplingStrategy
com.arosbio.ml.metrics.Metric
com.arosbio.ml.testing.TestingStrategy
```


(cpsign-extensions)=
## CPSign-API extensions

Apart from all extensions available under [ConfAI extensions](conf-ai-extensions) above, at the chemistry level CPSign can be extended with custom descriptors by adding your implementations of the interface `com.arosbio.cheminf.descriptors.ChemDescriptor`.


(encryption-extension)=
## Encryption extension
Generated models and precomputed datasets can be securely saved using instances of `com.arosbio.encryption.EncryptionSpecification` but there is no such implementation available in the public repo. Users can either purchase such an extension by contacting [Aros Bio](mailto:ola.spjuth@arosbio.com), or can implement their own encryption/decryption specifications and making them available as a Java service. Just note that currently a single implementation is allowed in the class path when running the CLI application, otherwise it will terminate in case encryption is specified. 
