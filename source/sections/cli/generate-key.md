(generate-key)=

# `generate-key`

The `generate-key` program is only usable in case an extension is added to CPSign, with an implementation of the `EncryptionSpecification` interface - either by purchasing this extension from [Aros Bio](mailto:ola.spjuth@arosbio.com) or by implementing one by yourself. This program can then be used to create an **encryption key** in either plain text or as a file containing the key. This key can then be used in {ref}`precompute` or {ref}`train` to encrypt data or models when they are saved to files. 


## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar generate-key
```

