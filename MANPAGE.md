<!-- DO NOT EDIT, AUTOMATICALLY GENERATED, EDIT dither.1.md INSTEAD -->
---
title: DIDDER
section: 1
header: User Manual
footer: didder v1.2.0
date: December 20, 2022
---

# NAME
didder — dither images

# SYNOPSIS
**didder** \[global options] command [command options] [arguments...]

# DESCRIPTION
Dither images with a variety of algorithms and processing options.

Images with transparency are supported, and their alpha channel is kept the way it was to begin with.

Mandatory global flags are **\--palette**, **\--in**, and **\--out**, all others are optional. Each command applies a dithering algorithm or set of algorithms to the input image(s).

The most important parts of this manual are highlighted in the **TIPS** section, make sure you check it out!

Homepage: <https://github.com/makeworld-the-better-one/didder>

# OPTIONS
**-i**, **\--in** *PATH*

Set the input file. This flag can be used multiple times to dither multiple images with the same palette and method. A *PATH* of \'**\-**' stands for standard input.

The input file path can also be parsed as a glob. This will only happen if the path contains an asterisk. For example **\-i \'\*.jpg'** will select all the .jpg files in the current directory as input. See this page for more info on glob pattern matching: <https://golang.org/pkg/path/filepath/#Match>

**-o**, **\--out** *PATH*

Set the output file or directory. A *PATH* of \'**\-**' stands for standard output. 

If *PATH* is an existing directory, then for each image input, an output file with the same name (but possibly different extension) will be created in that directory.

If *PATH* is a file, that ends in .gif (or **\--format gif** is set) then multiple input files will be combined into an animated GIF.

**-p**, **\--palette** *COLORS*

Set the color palette used for dithering. Colors are entered as a single quoted argument, with each color separated by a space. Colors can be formatted as RGB tuples (comma separated), hex codes (case-insensitive, with or without the '#'), a single number from 0-255 for grayscale, or a color name from the SVG 1.1 spec (aka the HTML or W3C color names). All colors are interpreted in the sRGB colorspace.

A list of all color names is available at <https://www.w3.org/TR/SVG11/types.html#ColorKeywords>

Images are converted to grayscale automatically if the palette is grayscale. This produces more correct results.

Here's an example of all color formats being used: **\--palette \'23,230,100 D24242 135 forestGreen'**

**-r**, **\--recolor** *COLORS*

Set the color palette used for replacing the dithered color palette after dithering. The argument syntax is the same as **\--palette**, with one exception. It also supports RGB*A* tuples, so 4 values. This means you can also choose to change the opacity of a palette color after dithering. The values are not premultiplied, so set the RGB to the color you want as you'd expect.

The **\--recolor** flag exists because when palettes that are severely limited in terms of RGB spread are used, accurately representing the image colors with the desired palette is impossible. Instead of accuracy of color, the new goal is accuracy of luminance, or even just accuracy of contrast. For example, the original Nintendo Game Boy used a solely green palette: <https://en.wikipedia.org/wiki/List_of_video_game_console_palettes#Game_Boy>. By setting **\--palette** to shades of gray and then **\--recolor**-ing to the desired shades of green, input images will be converted to grayscale automatically and then dithered in one dimension (gray), rather than trying to dither a color image (three dimensions, RGB) into a one dimensional green palette. This is similar to "hue shifting" or "colorizing" an image in image editing software.

For these situations, **\--recolor** should usually be a palette made up of one hue, and **\--palette** should be the grayscale version of that palette. The **\--palette** could also be just equally spread grayscale values, which would increase the contrast but make the luminance inaccurate.

Recoloring can also be useful for increasing contrast on a strange palette, like: **\--palette \'black white' \--recolor \'indigo LimeGreen'**. Setting just **\--palette \'indigo LimeGreen'** would give bad (low contrast) results because that palette is not that far apart in RGB space. These "bad results" are much more pronounced when the input image is in color, because three dimensions are being reduced.

**-s**, **\--strength** *DECIMAL/PERCENT*

Set the strength of dithering. This will affect every command except **random**. Decimal format is -1.0 to 1.0, and percentage format is -100% or 100%. The range is not limited. A zero value will be ignored. Defaults to 100%, meaning that the dithering is applied at full strength.

Reducing the strength is often visibly similar to reducing contrast. With the **edm** command, **\--strength** can be used to reduce noise, when set to a value around 80%.

When using the **bayer** command with a grayscale palette, usually 100% is fine, but for 4x4 matrices or smaller, you may need to reduce the strength. For **bayer** (and by extension **odm**) color palette images, several sites recommend 64% strength (written as 256/4). This is often a good default for **bayer**/**odm** dithering color images, as 100% will distort colors too much. Do not use the default of 100% for Bayer dithering color images.

**-j**, **\--threads** *NUM*

Set the number of threads used. By default a thread will be created for each CPU. As dithering is a CPU-bound operation, going above this will not improve performance. This flag does not affect **edm**, as error diffusion dithering cannot be parallelized.

**-g**, **\--grayscale**

Make input image(s) grayscale before dithering.

**\--saturation** *DECIMAL/PERCENT*

Change input image(s) saturation before dithering. Decimal range is -1.0 to 1.0, percentage range is -100% or 100%. Values that exceed the range will be rounded down. -1.0 or -100% saturation is equivalent to **\--grayscale**.

**\--brightness** *DECIMAL/PERCENT*

Change input image(s) saturation before dithering. Decimal range is -1.0 to 1.0, percentage range is -100% or 100%. Values that exceed the range will be rounded down.

**\--contrast** *DECIMAL/PERCENT*

Change input image(s) saturation before dithering. Decimal range is -1.0 to 1.0, percentage range is -100% or 100%. Values that exceed the range will be rounded down.

**\--no-exif-rotation**

Disable using the EXIF rotation flag in image metadata to rotate the image before processing.

**-f**, **\--format** *FORMAT*

Set the output file format. Valid options are \'png' and \'gif'. It will auto detect from filename when possible, so usually this does not need to be set. If **-o** is \'**-**' or a directory, then PNG files will be outputted by default. So this flag can be used to force GIF output instead. If your output file has an extension that is not .png or .gif the format will need to be specified.

**\--no-overwrite**

Setting this flag means the program will stop before overwriting an existing file. Any files written before that one was encountered will stay in place.

**-c**, **\--compression** *TYPE*

Set the type of PNG compression. Options are \'default', \'no', \'speed', and \'size'. This flag is ignored for non-PNG output.

**\--fps** *DECIMAL*

Set frames per second for animated GIF output. Note that not all FPS values can be represented by the GIF format, and so the closest possible one will be chosen. This flag has no default, and is required when animated GIFs are being outputted. This flag is ignored for non animated GIF output.

**-l**, **\--loop** *NUM*

Set the number of times animated GIF output should loop. 0 is the default, and will loop infinitely.

**-x**, **\--width** *NUM*

Set the width the input image(s) will be resized to, before dithering. Aspect ratio will be maintained if **\--height** is not specified as well.

**-y**, **\--height** *NUM*

Set the height the input image(s) will be resized to, before dithering. Aspect ratio will be maintained if **\--width** is not specified as well.

**-u**, **\--upscale** *NUM*

Scale image up after dithering. So \'2' will make the output two times as big as the input (after **-x** and/or **-y**). Only integers are allowed, as scaling up by a non-integer amount would distort the dithering pattern and introduce artifacts.

**-v**, **\--version**

Get version information.


# COMMANDS

**random** *MIN MAX* or *RED_MIN RED_MAX GREEN_MIN GREEN_MAX BLUE_MIN BLUE_MAX*

Grayscale and RGB random dithering

Accepts two arguments (min and max) for RGB or grayscale, or six (min/max for each channel) to control each RGB channel. Arguments can be separated by commas or spaces.

Random dithering adds random noise to the image. The min and max numbers limit the range of the random noise. A good default is -0.5,0.5, which means that a middle gray pixel is 50% likely to become black and 50% likely to become white, assuming a black and white palette. So -0.2,2.0 will reduce the noise (20%), while -0.7,0.7 will increase it (70%). Values like -0.5,0.7 will bias the noise to one end of the channel(s).

**-s**, **\--seed** *DECIMAL*

Set the seed for randomization. This will also only use one thread, to keep output deterministic. By default a different seed is chosen each time and multiple threads are used.

**bayer** *X* *Y*

Bayer matrix ordered dithering

Requires two arguments, for the X and Y dimension of the matrix. They can be separated by a space, comma, or \'x'. Both arguments must be a power of two, with the exception of: 3x5, 5x3, and 3x3.

**odm** *NAME/JSON/FILE*

Ordered Dithering Matrix

Select or provide an ordered dithering matrix. This only takes one argument, but there a few types available:

- A preprogrammed matrix name
- Inline JSON of a custom matrix
- Or a path to JSON for your custom matrix. \'**-**' means standard input.
   
Here are all the built-in ordered dithering matrices. You can find details on these matrices here: <https://github.com/makeworld-the-better-one/dither/blob/v2.0.0/ordered_ditherers.go>
   
- ClusteredDot4x4
- ClusteredDotDiagonal8x8
- Vertical5x3
- Horizontal3x5
- ClusteredDotDiagonal6x6
- ClusteredDotDiagonal8x8_2
- ClusteredDotDiagonal16x16
- ClusteredDot6x6
- ClusteredDotSpiral5x5
- ClusteredDotHorizontalLine
- ClusteredDotVerticalLine
- ClusteredDot8x8
- ClusteredDot6x6_2
- ClusteredDot6x6_3
- ClusteredDotDiagonal8x8_3
   
Their names are case-insensitive, and hyphens and underscores are treated the same.

The JSON format (whether inline or in a file) looks like the below. The matrix must be "rectangular", meaning each array must have the same length. More information how to use a custom matrix can be found here: <https://pkg.go.dev/github.com/makeworld-the-better-one/dither/v2#OrderedDitherMatrix>

```json
{
  "matrix": [
    [12, 5, 6, 13],
    [4, 0, 1, 7],
    [11, 3, 2, 8],
    [15, 10, 9, 14]
  ],
  "max": 16
}
```

**edm** *NAME/JSON/FILE*

Error Diffusion Matrix

Select or provide an error diffusion matrix. This only takes one argument, but there a few types available:

- A preprogrammed matrix name
- Inline JSON of a custom matrix
- Or a path to JSON for your custom matrix. \'**-**' means stdin.
   
Here are all the built-in error diffusion matrices. You can find details on these matrices here: <https://github.com/makeworld-the-better-one/dither/blob/v2.0.0/error_diffusers.go>
   
- Simple2D
- FloydSteinberg
- FalseFloydSteinberg
- JarvisJudiceNinke
- Atkinson
- Stucki
- Burkes
- Sierra (or Sierra3)
- TwoRowSierra (or Sierra2)
- SierraLite (or Sierra2_4A)
- StevenPigeon
   
Their names are case-insensitive, and hyphens and underscores are treated the same.

The JSON format (whether inline or in a file) for a custom matrix is very simple, just a 2D array. The matrix must be "rectangular", meaning each array must have the same length.

**-s**, **\--serpentine**

Enable serpentine dithering, which "snakes" back and forth when moving down the image, instead of going left-to-right each time. This can reduce artifacts or patterns in the noise. 

# TIPS

Read about **\--strength** if you haven't already.

Read about **\--recolor** if you haven't already.

It's easy to mess up a dithered image by scaling it manually. It's best to scale the image to the size you want before dithering (externally, or with **\--width** and/or **\--height**), and then leave it.

If you need to scale it up afterward, use **\--upscale**, rather than another tool. This will prevent image artifacts and blurring.

Be wary of environments where you can't make sure an image will be displayed at 100% size, pixel for pixel. Make sure to at least use nearest-neighbor scaling, do your best to preserve sharp pixel edges.

Dithered images must only be encoded in a lossless image format. This is why the tool only outputs PNG and GIF.

To increase the dithering artifacts for aesthetic effect, you can downscale the image before dithering and upscale after. Like if the image is 1000 pixels tall, your command can look like **didder --height 500 --upscale 2 [...]**. Depending on the input image size and what final size you want, you can of course just upscale as well.

If your palette (original or recolor) is low-spread — meaning it doesn't span much of the available shades of a single hue or the entire RGB space — you can use flags like **\--brightness**, **\--contrast**, and **\--saturation** to improve the way dithered images turn out. For example, if your palette is dark, you can turn up the brightness.  As mentioned above, these flags apply their transformations to the original image and will not adjust your selected palette colors.

# EXAMPLES

**didder \--palette \"black white" -i input.jpg -o test.png bayer 16x16**


This command dithers `input.jpg` using only black and white (implicitly converting the image to grayscale first), using a 16x16 Bayer matrix. The result is written to `test.png`.

**didder \--palette \"black white" -i input.jpg -o test.png odm ClusteredDot4x4**


Same command as above, but dithering with the preprogrammed ordered dithering matrix called ClusteredDot4x4.

**didder -i david.png -o david_dithered.png \--palette \"black white" \--recolor \"black F273FF" \--upscale 2 bayer 4x4**


This is the command used for the README. It dithers using a 4x4 Bayer matrix, initially to black and white, which is then recolored to black and purple. Dithering to black and purple directly would produce much lower contrast results. The dithered image is upscaled to be two times larger, so that the Bayer dithering artifacts can be seen more clearly.

**didder -i input.png -o output.png -p \"1E1E1E CDCDCD EDEDED FFFFFF" -r \"11161e 116bcd 63b3ed e1efff" \--strength 64% \--brightness 20% bayer 32x32**


This command uses a blue recolor palette, one that is biased to being darker. The palette can be viewed at <https://colorpeek.com/#11161e,116bcd,63b3ed,e1efff>. The dithering palette is the grayscale version of those colors, to keep luminance accurate. Strength is set to 64%, which although usually recommended for Bayer dithering of color images, works well here. Alternatively, one could try and increase **\--contrast**. Finally, the brightness is increased to compensate for the dark palette.

**didder -p \"black white" \--recolor \"darkgreen white" -i frame_01.png -i frame_02.png -o output.gif \--fps 1 random -0.5,0.5**


This command takes two input images and creates an animated GIF, dithering and recoloring them along the way. The GIF moves at 1 frame per second, and by default loops infinitely. Random dithering is used, with recommended default of -0.5,0.5.

# REPORTING BUGS

Any bugs can be reported by creating an issue on GitHub: <https://github.com/makeworld-the-better-one/didder>
