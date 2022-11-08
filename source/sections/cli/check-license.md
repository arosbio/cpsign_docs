---
substitutions:
  br: |-
    ```{raw} html
    <br />
    ```
---

(checklicense)=

# Check-license

The check-license program reads a license and outputs information about it like date of expiration, who the license is registered to, type of license and version of the software it covers.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Parameters

The full usage menu can be retrieved by running command:

```text
> java -jar cpsign-[version].jar check-license -h

                                      check-license
SYNOPSIS
------------------------------------------------------------------------------------------
  check-license [options]
  check-license @/tmp/runconfigs/parameters.txt [options]
  check-license @C:\Users\User\runconfigs\parameters.txt [options]


DESCRIPTION
------------------------------------------------------------------------------------------
  Get information about a CPSign license. Returns information about who the license is
  registered to, type of license, when it expires, version and if the license can be used
  for encryption.


OPTIONS
------------------------------------------------------------------------------------------
  Input:
  * -l  | --license                          [URI | path]
       Path or URI to license file

  Output:
    -rf | --result-format                    [id | text]
       Output format, options:
         (1) json
         (2) text | plain
       Default: 2

  General:
    -h  | --help | man
       Get help text
    --short
       Use shorter help text (used together with the --help argument)
    --logfile                                [path]
       Path to a user-set logfile, will be specific for this run
    --silent
       Silent mode (only print output to logfile)
    --echo
       Echo the input arguments given to CPSign

------------------------------------------------------------------------------------------
```

## Example Usage

```bash
> java -jar cpsign-[version].jar check-license \
   --license /path/to/Predict-license.license

License Info:
-------------
License type:             Predict
Registered Owner:         Staffan Arvidsson
Registered Company:       Genetta Soft AB
License expiration date:  Wed Apr 24 13:34:48 CEST 2019
License version:          0.5.0
Supports encryption:      No

> java -jar cpsign-[version].jar check-license \
   --license /path/to/Standard-license.license \
   -rf 1

{
     "licenseExpirationDateAbsTime": 1556105786531,
     "licenseType": "Standard",
     "registeredCompany": "Genetta Soft AB",
     "supportsEncryption": false,
     "licenseExpirationDate": "Wed Apr 24 13:36:26 CEST 2019",
     "licenseVersion": "0.5.0",
     "registeredOwner": "Staffan Arvidsson"
}
```
