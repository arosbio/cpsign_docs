
(input_formats)=

# Input formats in CPSign

```{contents} Table of Contents
:depth: 3
```

## Raw - numerical data

CPSign loads and stores numerical data in LIBSVM/Liblinear file format:

```bash
<label> <index>:<value> <index>:<value> ..
<label> <index>:<value> <index>:<value> ..
..
```

Also note that the `<index>` **must start at 1** and not 0, to conform with LIBSVM/LibLinear requirements.

## Chemical data
### CSV file format

CPSign supports CSV files in a fairly flexible manner, allowing to specify the separator characters and other parameters that might differ between formats.
Molecules should be encoded as SMILES strings and there is a requirement that the CSV must either contain an explicit header row - or the user must specify the header from either the CLI or Java API - so that all fields can be located. There **must** exist a header containing the text "smiles" (case insensitive), the first header containing "smiles" will be taken as SMILES column. Example of a supported CSV file, using tab as delimiter:

```text
SMILES   Sample_ID   Activity Additional_Notes
OC(=O)\C=C/C(O)=O.C[C@]12CC=C3[C@@H](CCC4=CC(=O)C=C[C@]34C)[C@@H]1CC[C@@H]2C(=O)CN1CCN(CC1)C1=NC(=NC(=C1)N1CCCC1)N1CCCC1   NCGC00261900-01   POS   Here's some additional information
[Na+].NC1=NC=NC2=C1N=C(Br)N2C1OC2CO[P@]([O-])(=O)O[C@@H]2C1O   NCGC00260869-01   NEG   More notes
O=C1N2CCC3=C(NC4=C3C=CC=C4)C2=NC2=C1C=CC=C2  NCGC00261776-01   NEG
Cl.FC1=CC=C(C=C1)C(OCCCC1=CNC=N1)C1=CC=C(F)C=C1 NCGC00261380-01   POS
CC1=CC=C(C=C1)S(=O)(=O)N[C@@H](CC1=CC=CC=C1)C(=O)CCl  NCGC00261842-01   NEG   Not all lines need to contain the additional notes
...
```
The column header for SMILES can contain more text, e.g. "canonical smiles" and "smiles-col" are both valid header names.
### SDF

SDFiles are supported in both v2000 and v3000.

### SMILES as single molecule

The {ref}`predict` and {ref}`online-predict` command can predict single molecules using the `--smiles` parameter, this parameter takes a string of text where the string must start with a valid SMILES and can then optionally include a blank space character (tab, space) and an identifier.

### JSON file format

CPSign supports a [JSON](http://www.json.org/) input format, the format require that the top level starts as a JSON array (meaning that the first
character must be a hard bracket "`\[`"). Each index of the array is one record and each record **must** include a key-value
for SMILES for the molecule. This SMILES key-value pair must have the key "SMILES", "smiles" or "Smiles". Here are some
examples for the file format (it is not required that the file is properly indented).

Example classification JSON file:

```json
[
   {
      "cdk:Title" : "1728-95-6",
      "Ames test categorisation" : "mutagen",
      "smiles" : "C1(=C(C=2C=CC=CC2)N=C(N1)C3=CC=C(OC)C=C3)C=4C=CC=CC4"
   },

   {
      "cdk:Title" : "91-08-7",
      "Ames test categorisation" : "mutagen",
      "smiles" : "C=1(C(=C(C=CC1)N=C=O)C)N=C=O"
   }
]
```

Example regression JSON file:

```json
[
   {
      "BIO" : "0.43",
      "comment" : "This is a comment",
      "smiles" : "SC1=C(C(F)(F)F)C=CC=C1"
   },

   {
      "BIO" : "1.60",
      "comment" : "Comment for second molecule",
      "smiles" : "SC1=C(C(F)(F)F)C=C([N+]([O-])=O)C=C1"
   }
]
```

## Compressed input files

CPSign automatically reads files compressed in GZIP format.

