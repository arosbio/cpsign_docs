(explain)=

# `explain`

To make it easier to work with the CLI without requiring going back and forth to this website to get more detailed explanations about parameters, the `explain` program can give more detailed information about parameters than given in the normal usage texts. Sometimes the usage texts give information about the exact command to run to get information, but otherwise you can run `explain` without any arguments or `explain --help` to check which parameters or key terms that can be used by this program.


## Example
Here is an example of how to get information about the available scoring algorithms and all available hyper-parameters thereof.
```bash
> ./cpsign-[version]-fatjar.jar explain scorer
```

Further note that `explain` loads information using the Java Service loader functionality just as the rest of CPSign - so any custom extensions that you made available should be included in these outputs - which can be a convenient way of making sure you set up everything correctly and your extension is available for you to use.
