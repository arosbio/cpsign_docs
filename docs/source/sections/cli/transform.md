
(transform)=

# `transform`

The `transform` program can optionally be used in order to separate the descriptor calculation in {ref}`precompute` and data transformations into separate steps. In this way several different transformation approaches can be compared but save computational resources by performing the descriptor calculation once.

## Usage manual

The full usage manual can be retrieved by running command:

```bash
> ./cpsign-[version]-fatjar.jar transform
```

## Picking transformations

The available transformations can be found running:
```bash
> ./cpsign-[version]-fatjar.jar explain transformations
```

