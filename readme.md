# CAD JS (Version 0.6) - Pure Javascript 2D CAD drawing program
This application uses pure JS to allow for 2D CAD drawing. It was created not to solve problems with existing programs, but as an exercise in using nothing but pure Javascript to accomplish something useful. Some items are needless use of Canvas drawing, such as vertexes, icons, and the UI. Those are intended as placeholders.

Please let me know if and how anything should/could improve, such as: best practices, data structures, usability, etc.

NOTE: **All files are written in MS Notepad**. This is part of the exercise - I am forced to write things with no assistance from an IDE. Thus, the JS files are saved as .txt and I don't intend this to stay this way.

## Using CAD JS
Most tools are built to be self-explanatory with the exception of the selector tool. The selector selects and unselects vertexes as expected, but to move the selected vertexes CTRL must be held.

Holding CTRL activates the "secondary" function of any tool you're using, if it exists. Your cursor will change to a red PLUS (placeholder).

Shift is used to add more vertexes to the current selection set.

## Future
I have plans to expand the usability of CAD JS. These include (in order of importance):

1. More tools, such as ~~Arc~~, Spline, ~~N-Gon~~, Dimension, Text, and others as I think of them

   These will all need rewriting as I refactor with Three.js as the new backbone.
   Hopefully this process will expose inefficiencies as it tears through the existing code.

2. Tooltips to explain how to use CAD JS
3. Add constraints, such as Coincident, Horizontal/Vertical, Dimension, Fix, etc.
4. Canvas efficiency upgrades - more effective usage of Canvas' drawing tools

   After investigation, I think I will be using two libraries for this: [Three.js](https://github.com/mrdoob/three.js "Three.js") for rendering and [Require.js](https://github.com/requirejs/requirejs "Require.js") to handle my inefficiently separated classes by files.
   I will be using minified versions of both so I can continue my masochistic pursuit of creating this thing in notepad.

5. Exporting to various filetypes, such as DXF, DWG, PDF, PNG
6. Saving drawings in a native filetype to a local location for retrieval later

# Changelog

## Version 0.5

+Initial feature incomplete release

## Version 0.6
+Major migration to using libraries ([Three.js](https://github.com/mrdoob/three.js "Three.js") and [Require.js](https://github.com/requirejs/requirejs "Require.js")) begun