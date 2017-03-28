# Photoshop for Beginners

Useful shortcuts:
- `Ctrl + Spacebar`: Zoom in/out. Move the mouse around to control the extend of the zoom.
- `F12`: Reverts the image completely. Useful for before/after comparisons, otherwise just use the history tool.

## Opening and Saving Files

`File -> Save for web`

When you save for web, you can specify the width and height there (instead of doing it as a separate action before saving)

Can open files by double clicking on an empty workspace

Can drag images in explorer into photoshop for the same effect

`File -> Browse in Bridge` allows you to preview files you can’t normally preview. Consider it an advanced way of previewing your images.

## Making Selections

You should memorize the zoom options found under the `View` menu.

`Ctrl +`: Zoom in
`Ctrl -`: Zoom out
`Ctrl + 0`: Fit on screen
`Ctrl + 1`: Actual pixels

### Marquee Tool

The tool with the dashed rectangle icon is called the marquee tool

If you hold down `Shift`, your selection will be a square

Can start a selection from the center by holding `Alt` then dragging

Can combine the two feature by holding `Shift + Alt` then dragging

If you right click the marquee tool, you can select different shapes to use for making your selection

Gaussian blur is one of the best blurs photoshop has

`Alt + Shift` & dragging works for the other shapes as well

With a selection already made, hold `Shift` or `Alt` and make another selection to add or subtract from the current selection

In the above, holding `Alt` + `Shift` gives the intersection

### Lasso Tools

Polygon lasso tool is pretty neat. I'm not clear on how to end a polyon lasso selection though.

Magnetic lasso tool is awesome

Double click to finish a selection or press `ESC` to cancel the selection

To de-select, click away or use `Ctrl + D`

To pan your image around, hold `Spacebar` and drag. Doesn’t mess with your current selection!

## Advanced Selections and Refining

This section covers how to remove a person's shadow from an image

The magic wand tool selects the range of colors that it's connected to. It makes some pretty awesome automatic selections.

When making a magic wand selection, you can adjust its tolerance setting

The quick selection tool is incredible. Use it along with `Shift` to incrementally add selections. Can select the outline of a person in an image in seconds doing this!

While making a quick selection, there is an option below the main menu to `Refine Edge...`

Check `View: On Black` to get a proper view of your selecion

Check `Smart Radius` and begin increasing the radius. Using the quick selection tool, draw on some missed sections and that will get picked up by the smart radius as well.

`Select -> Inverse` inverts the selection

When you select the Paintbrush tool, there's a secondary menu that allows you to change the brush size

At the bottom of the left toolbar you'll see two squares. Change the color of the top-left square in order to change the color of the brush. Can also click the double arrow icon to switch the primary and secondary colors.

What's cool is that you don't even have to be precise with the brush because it only affects the selected area. If you have the person selected with the quick selection tool, then invert the selection, you can use the paintbrush on the person and it won't do anything!

Quick selection + paintbrush is honestly an amazing combination

## Cropping Images

Cropping is as simple as making a selection with the marquee tool then doing `Image -> Crop`

Can also use the cropping tool. When you're happy with your crop, you can either hit `Enter` or double click to perform the crop.

There's an option in the cropping submenu to crop to a specific size and resolution

The arrow next to the aspect ratio values allows you to invert the aspect ratio

Not clear on how to remove the crop outline after already making a selection

## Straightening Images

The ruler tool has a submenu option called `Straighten Layer`. If you draw a crooked line on the image and use this option, it will straighten the image such that the line now appears perfectly horizontal.

Author claims this option also automatically crops the image. This didn't happen on my end.

## Red Eye Removal

One trick is to use the Paintbrush tool, switch the mode to Saturation and paint over the eye. This offers a "passable"  though very simple solution.

Photoshop also has a Red Eye Tool specifically designed for this task. All you need to do is zoom in on the eye and click once. There are also submenu options for `Pupil Size` and `Darken Amount`.

An old school trick is to place a dark circle with a white dot using the paintbrush tool

## Photo Color Filters

`Image -> Adjustments -> Photo Filter`

Experiment with a combination of different filters and Density percentages. Can even choose your own color to use as a filter.

Hit `Ctrl + Z` repeatedly to compare the before & after

## Fixing Images with the Clone Tool

Using the `Clone Stamp Tool`, hold `Alt` and select a person. Click somewhere else on the screen and paint the cloned person (notice you have 2 cursors when doing this, one on the original person).

Can apply the same principles to remove a person from an image. In that case, you hold `Alt` and click on a sample of the background that should replace the person.

## Content Aware Filling

- Use the lasso or selection tool to make a selection of the item you want to remove
- `Edit -> Fill -> Use: Content-Aware`
- Be amazed

## Layers Palette

The default layer is the `Background` layer, and it is locked. To unlock it, simply double click the layer and change its name. Can now toggle the visibility of the layer by clicking on the "eye" icon.

Can combine the use of the quick selection tool with other selection tools such as the lasso

Layer has its own menu dedicated to it

After making a selection, use `Ctrl + Shift + J` to split the layer into two (the selection and the inverse of the selection)

Notice there are 4 icons next to the word `Lock:` Can lock by transparency, paint brush, movement, and full lock.

When a layer is locked, you can do things like painting with the paintbrush while only affecting that layer.

If you click the figure 8 icon, it links two layers together. Allows you to do things like moving the layers simultaneously.

When the layers are separated, you can move a layer around with the `Move Tool`

To duplicate a layer, simply drag it down to the icon next to the trash can

## Masking Images

You can duplicate the background layer

`Filter -> Blur -> Radial Blur`, choose the `Zoom` option, and play around with the blur amount and blur cetner

Can change the size of your brush (or eraser) using the bracket keys `[` and `]`

If you create a background and a copy of it with the blur effect, then un-view the original background, anywhere you erase on the blurred layer will appear normally when both layers are used

An even better technique is to add a mask to the image. This option can be found at the bottom of the layer panel, it looks like a camera icon.

Using the brush with a black color, color over the region you want to be un-blurred. If you make a mistake, simply switch the color to white and re-draw. Notice that next to the layer in the layer panel, you'll see another thumbnail showing the results of your mask.

You can even use the brush while both layers are on

## Using Levels

`Image -> Adjustments -> Levels` or `Ctrl + L`

Play around with the 3 sliders in the `Input Levels` graph to adjust the brightness of the image

Can also use eye droppers to sample colors from the image. The eye droppers have the same ordering as the sliders: dark, middle (gray), white. So when you make a selection with the dark eye dropper, you're telling the image that you want the selected color to be the baseline black color, and adjust all the other colors accordingly.

You can also use the `Auto` option, then make further manual adjustments

If you change the `Channel` from `RGB`, you can target individual colors

Since it's easy to make mistakes with level adjustments, it's a good idea to make adjustment layers

In the layers panel, there's a semicircle icon in the middle. Clicking on it shows a bunch of options, one of which is Levels.

When you're done making adjustments, you can press the `x` in the top-right corner of the new window and it will still remember your changes. If you want to make further adjustments, simply double click the adjustment layer.

## Transforming Images

To add an image as a new layer, do `File -> Place` and browse to the image

If you hold `Ctrl`, you can move a layer even if you don't have the Move tool selected

Once the logo is placed where you want it, go to `Edit -> Free Transform`

If you go outside the corners of the free transform box, you get the option to rotate the image

When you're done, hit enter, and optionally change the opacity on the image layer

And just like that, you can do things like add a logo to an image

## Adding Text to an Image

In the text submenu, you can place your mouse over the T, hold left click and drag to dynamically change the font size

Can also press `Ctrl` and resize the resulting box around the text

When you add new text (i.e. you see the bullet point), you can move the text around by clicking anywhere outside of it

The option to change the font color is found in the color rectangle in the text submenu

While changing colors, you can actually sample colors in the image and set your text color to that. I love this feature!

## Resizing Images

Anything you can do with the Canvas tool, you can do with the Crop tool as well

Can actually widen an image using the crop tool. The extra space will be filled with a white background.

## Creating Panoramic Images

`File -> Automate -> Photomerge` then select all the images you want to merge together

`Layer -> Merge Visible` to combine all the created layers into one

For the missing sections, use the lasso tool + `Edit -> Fill -> Content Aware`