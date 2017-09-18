# CAD JS - Pure Javascript 2D CAD drawing program
This JS application uses pure JS to allow for 2D CAD drawing. It was created not to solve problems with existing problems, but as an exercise in using nothing but pure Javascript to accomplish something useful. Some items are needless use of Canvas drawing, such as vertexes, icons, and the UI. Those are intended as placeholders.

Please let me know if and how anything should/could improve, such as: best practices, data structures, usability, etc..

NOTE: All files are written in MS Notepad. This is part of the exercise - I am forced to write things correctly and remember variable names. Thus, the JS files are saved as .txt and I don't intend this to stay this way.

## Using CAD JS
Most tools are built to be self-explanitory with the exception of the selector tool. The selector selects and unselects vertexes as expected, but to move the selected vertexes CTRL must be held.

Holding CTRL activates the "secondary" function of any tool you're using, if it exists. Your cursor will change to a red PLUS (placeholder).

Shift is used to add more vertexes to the current selection set.

## Future
I have plans to expand the usability of CAD JS. These include (in order of importance):

1. More tools, such as Arc, Spline, N-Gon, Dimension, Text, and others as I think of them
2. Tooltips to explain how to use CAD JS
3. Add constraints, such as Coincident, Horizontal/Vertical, Dimension, Fix, etc.
4. Canvas efficiency upgrades - more effective usage of Canvas' drawing tools
5. Exporting to various filetypes, such as DXF, DWG, PDF, PNG
6. Saving drawings to a local location for retrieval later