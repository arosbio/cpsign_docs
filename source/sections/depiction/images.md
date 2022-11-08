---
substitutions:
  br: |-
    ```{raw} html
    <br />
    ```
---

(images)=

# Image rendering

CPSign has functionality for rendering and generating molecule images.

```{contents} Table of Contents
:backlinks: top
:depth: 3
```

## Image Creation

CPSign comes with the possibility of generating images for predicted molecules in two ways:

**Color complete molecule gradient** The coloring is based on computing the full molecular gradient,
meaning that all signatures respective contribution are taken into account and then tracked back to the atoms
each signature stems from. This will thus color contribution for each atom in the molecule.

**Color Significant Signature only** The coloring is based on the Significant Signature only. Atoms that are part of
the Significant Signature will be colored after a given highlight color. When using the CLI, the significant signature
will be calculated using changes of the highest p-value for ACP classification, when using the API the
calculation can be done on either of the classes.

## Image rendering on Command Line

Both types of images can be generated at the same run, each controlled by their own parameters.
Image size, atom numbering, legends, output files and coloring are all configurable by parameters as can
be seen in the sections below.

### Gradient images

The gradient options are the following:

```text
> java -jar cpsign-[version].jar predict gradient

                                         predict
------------------------------------------------------------------------------------------
  Gradient image output:
    -gi | --gradient-images
       Create a Gradient image for each predicted molecule.
    -if | --image-file                       [path]
       Path to where generated images should be saved, can either be a path to a specific
       folder or a full path including a file name (only .png file ending supported).
       Every image will be named '[name]-[count].png' or '[name]-[$cdk:title].png' where
       name is either a default name or the specified name to this parameter (e.g. '.' -
       current folder using default file name, '/tmp/imgs/DefaultImageName.png' - use
       /tmp/imgs/ as directory and use 'DefaultImageName' as file name)
       Default: imgs/GradientDepiction.png
    -cs | --color-scheme                     [text]
       The specified color-scheme (case in-sensitive), options:
         (1) blue:red
         (2) red:blue
         (3) red:blue:red
         (4) cyan:magenta
         (5) rainbow
             custom - contact Aros Bio for custom requirements!
       Default: 1
    --color-legend
       Add a color legend at the bottom of the image
    --atom-numbers
       Depict atom numbers
    --atom-number-color                      [color name] | [hex color]
       Color of the atom numbers
       Default: BLUE
    -ih | --image-height                     [text]
       The height of the generated images (in pixels)
       Default: 400
    -iw | --image-width                      [integer]
       The width of the generated images (in pixels)
       Default: 400
```

Coloring is configured using the `--color-scheme` parameter and the possible values can be seen in [Color schemes] section.

### Significant signature images

The significant signature options are the following:

```text
> java -jar cpsign-[version].jar predict significant

                                         predict
------------------------------------------------------------------------------------------
  Significant Signature image output:
    -si | --signature-images
       Create a Significant Signature image for each predicted molecule
    -sf | --signature-image-file             [path]
       Path to where generated images should be saved, can either be a path to a specific
       folder or a full path including a file name (only .png file ending supported).
       Every image will be named '[name]-[count].png' or '[name]-[$cdk:title].png' where
       name is either a default name or the specified name to this parameter (e.g. '.' -
       current folder using default file name, '/tmp/imgs/DefaultImageName.png' - use
       /tmp/imgs/ as directory and use 'DefaultImageName' as file name)
       Default: imgs/SigificantSignatureDepiction.png
    -hc | --highlight-color                  [color name] | [hex color]
       The color that should be used for the highlighting of the significant signature
       Default: BLUE
    --signature-color-legend
       Add a color legend at the bottom of the image
    --signature-atom-numbers
       Depict atom numbers
    --signature-atom-number-color            [color name] | [hex color]
       Color of the atom numbers
       Default: BLUE
    -sh | --signature-image-height           [text]
       The height of the generated images (in pixels)
       Default: 400
    -sw | --signature-image-width            [integer]
       The width of the generated images (in pixels)
       Default: 400
```

Instead of a color scheme as for gradient images, the significant signature has a highlight color
(`--highlight-color`) that decides the color of the significant signature.

### Example Images

**Significant Signature, using:** {{ br }}

```bash
> java -jar cpsign-[version].jar predict \
   --signature-images \
   --highlight-color 'RED' \
   ...
```

```{image} imgs/SignificantSignature.png
:align: center
:scale: 50%
```

**Significant Signature only with color legend, using:** {{ br }}

```bash
> java -jar cpsign-[version].jar predict \
   --signature-images \
   --highlight-color 'RED' \
   --signature-color-legend \
   ...
```

```{image} imgs/SignificantSignatureWithLabel.png
:align: center
:scale: 50%
```

**Molecule gradient with rainbow color scheme, using:** {{ br }}

```bash
> java -jar cpsign-[version].jar predict \
   --gradient-images \
   -cs 'rainbow' \
   --color-legend \
   ..
```

```{image} imgs/FullGradientWithRainbow.png
:align: center
:scale: 50%
```

**Molecule gradient with color legend and atom numbers using:** {{ br }}

```bash
> java -jar cpsign-[version].jar predict \
   --gradient-images
   -cs "red:blue:red" \
   --color-legend \
   --atom-numbers \
   --atom-number-color 'BLACK' \
   ..
```

```{image} imgs/FullGradientWithLegendAtomNr.png
:align: center
:scale: 50%
```

(color-schemes)=

## Color schemes

Color schemes supported by CPSign (both API and CLI).

**BLUE:RED (Default)**

```{image} imgs/BlueRedGradient.png
:align: center
```

**RED:BLUE**

```{image} imgs/RedBlueGradient.png
:align: center
```

**RED:BLUE:RED**

```{image} imgs/RedBlueRedGradient.png
:align: center
```

**CYAN:MAGENTA**

```{image} imgs/CyanMagentaGradient.png
:align: center
```

**'Rainbow' gradient**

```{image} imgs/RainbowGradient.png
:align: center
```

## Image rendering in API

Image rendering in the API can be done in two basic ways;

1. {code}`MoleculeDepictor`: Only renders java [BufferedImage] of the molecule, including highlighting. Very flexible but forces you to do everything but the depiction yourself.
2. {code}`GradientFigureBuilder` and {code}`SignificantSignatureFigureBuilder` classes: Builder classes that allows to set fields under and over the molecule, also supports rendering the figure as a java [BufferedImage], Base64 String or saved it as PNG file.

Regardless if you choose the builder classes or the {code}`MoleculeDepictor`, you will have to take care of how to color the atoms. When you calculate the
gradient of a molecule with {code}`predictSignificantSignature()` method you will get a both a set of atoms that are part of the Significant Signature and a map
for the full molecule gradient ({code}`IAtom` to a floating point value). If you wish to use the
full molecule gradient, you will have to make sure that the floating point value is normalized (otherwise the depiction will likely not give you much information). If the
model was trained through the CLI this will have been done for you, otherwise you will have to either fix this somehow yourself or by calling the {code}`computePecentiles()` method:

```java
// Load data
signACP.fromMolsIterator(iterator,
            "Ames test categorisation",
            new NamedLabels(Arrays.asList("nonmutagen", "mutagen")));

// Train
signACP.train();

// Compute percentiles
signACP.computePercentiles(iterator);
```

In the CLI implementation, CPSign uses the training data set to compute the lower and upper ranges, but as a API user you are free to run any dataset
to compute percentiles (e.g. use the test dataset). The percentiles will in either way be used for normalizing the output in a model and dataset-dependet way.
Your free to use any other normalization if you would like (e.g. a per molecule normalization).

### MoleculeDepictor

If you wish to have flexibility in the depiction, for instance creating a larger report with multiple molecule depictions, render your image on a
specific background (e.g. with a water-mark) or include a color coding bar, you can use the {code}`MoleculeDepictor` class.
The {code}`MoleculeDepictor` renders images and returns them as a [BufferedImage], giving you all the flexibility that you could desire.
The general usage:

```java
// Get an instance of the MoleculeDepictor with the IColorGradient you wish to use
MoleculeDepictor mp = MoleculeDepictor.getBloomDepictor(GradientFactory.getRainbowGradient());

// Set the background
BufferedImage pageImage = new BufferedImage(mp.getImageWidth(), mp.getImageHeight(), BufferedImage.TYPE_INT_RGB);
Graphics2D graphics = pageImage.createGraphics();
graphics.setColor(Color.WHITE);
graphics.fillRect(0, 0, width, height);

// Get a BufferedImage of the rendered molecule
BufferedImage img = mp.depict(molecule, coloringMap);

graphics.drawImage(img, 0,0,null);

// Save the image as a file
ImageIO.write(pageImage, "png", new File("/tmp/image.png");
```

There {code}`GradientFactory` generates gradients these factory methods:

- *getDefaultBloomGradient*
- *getBlueRedGradient*
- *getRedBlueGradient*
- *getRedBlueRedGradient*
- *getCyanMagenta*
- *getRainbowGradient*
- *getCustomGradient(String)*

It's also fully possible to implement the {code}`IColorGradient` interface if you wish to make your own.

The {code}`MoleculeDepictor` has two factory methods for getting an instance of the class:

- *getBloomDepictor*
- *getBloomDepictor(IColorGradient)*

There is a working example of how to generate customized images our [GitHub] repository, in the class {code}`GeneratePredictionImages.java`. That code currently
depicts this image:

```{image} imgs/subplots.png
:align: center
```

### MoleculeFigureBuilder classes

The two classes {code}`GradientFigureBuilder` and {code}`SignificantSignatureFigureBuilder` are new to v0.7.0 of CPSign
and replaces the old {code}`MolImageDepictor` class. These now come with quite a few configurations and is much more
configurable. Once you call the {code}`build()` method you get a {code}`MoleculeFigure` that holds the rendered BufferedImage
and supports there output formats:

- Java [BufferedImage]
- Base64 String
- PNG file

The usage is fairly straight forward (example taken from examples in [GitHub]):

```java
// Instantiation requires a MoleculeGradientDepictor
GradientFigureBuilder gradBuilder = new GradientFigureBuilder(
     new MoleculeGradientDepictor(
             GradientFactory.getRainbowGradient()));

// Set a title
String titleText = "Rainbow gradient";
AttributedString title = new AttributedString(titleText);
// See available attributes at: https://docs.oracle.com/javase/8/docs/api/java/awt/font/TextAttribute.html
title.addAttribute(TextAttribute.UNDERLINE, TextAttribute.UNDERLINE_ON, 0, titleText.length()); // Add underline
title.addAttribute(TextAttribute.FOREGROUND, Color.RED, 0, 7); // Add red text for "Rainbow"
title.addAttribute(TextAttribute.POSTURE, TextAttribute.POSTURE_OBLIQUE, 0, 7); // Italics for "Rainbow"
TitleField tf = new TitleField(title);
tf.setAlignment(Vertical.LEFT_ADJUSTED); // default is a centered title
gradBuilder.addFieldOverImg(tf);

// Add a boarder around the molecule
Boarder molBoarder = new Boarder(BoarderShape.ROUNDED_RECTANGLE, new BasicStroke(3f), Color.BLUE);
gradBuilder.getDepictor().addLayout(new CustomLayout(new Padding(0), molBoarder, new Margin(5)));

gradBuilder.addFieldUnderImg(new ColorGradientField(gradBuilder.getDepictor())); // Color legend

// Set figure size
gradBuilder.setFigureHeight(550);
gradBuilder.setFigureWidth(499);

// Build the figure
MoleculeFigure fig = gradBuilder.build(testMol, ss.getMoleculeGradient());
fig.saveToFile(new File(Configuration.IMAGE_BASE_PATH, "FullGradientRainbow.png"));
```

The resulting image:

```{image} imgs/FullGradientRainbow.png
:align: center
```

Usage of the {code}`SignificantSignatureFigureBuilder` is equivalent except instantiation and the arguments to {code}`build()`
which instead takes a set of {code}`IAtoms` that should be highlighted.

### MoleculeFigure dimensions

Image dimension can both be set on the {code}`MoleculeDepictor` and on the {code}`MoleculeFigureBuilder`, where the latter has
precedence over the former. In case no size is explicitly set on the {code}`MoleculeFigureBuilder`, the molecule depiction will
have the dimensions set on the {code}`MoleculeDepictor` or the default dimensions. Any extra fields and layouts will
be added on top of the molecule depiction and generate a larger final {code}`MoleculeFigure` in case extra fields has been
added.

[bufferedimage]: https://docs.oracle.com/javase/7/docs/api/java/awt/image/BufferedImage.html
[github]: https://github.com/arosbio/cpsign-examples
