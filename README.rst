python_pptx_interface
=====================
.. image:: https://img.shields.io/pypi/v/python_pptx_interface.svg
    :target: https://pypi.org/project/python_pptx_interface/

.. image:: http://img.shields.io/:license-MIT-blue.svg?style=flat-square
    :target: http://badges.MIT-license.org

|

.. figure:: https://github.com/natter1/python_pptx_interface/raw/master/docs/images/general_example_o1_title_slide.png
    :width: 500pt

|

`python-pptx <https://github.com/scanny/python-pptx.git>`_ is a great module to create pptx-files.
But it can be challenging to master the complex syntax. This module tries to present an easier interface
for python-pptx to create PowerPoint files. It also adds some still missing features like moving slides,
create links to other slides or remove unused place-holders. If matplotlib is installed, it can be used,
to write formulas with latex syntax. And if PowerPoint is available, a presentation could be exported as
pdf or png(s).

Content
-------

  * `class PPTXCreator <#class-pptxcreator>`__: Create pptx-File from template, incluing methods to add text, tables, figures etc.
  * `class PPTXPosition <#class-pptxposition>`__: Allows positioning as fraction of slide height/width.
  * `Style sheets <#style-sheets>`__
     + `class PPTXFontStyle <#class-pptxfontstyle>`__: Helps to set/change/copy fonts.
     + `class PPTXParagraphStyle <#class-pptxparagraphstyle>`__: Format paragraphs (alignment, indent ...).
     + `class PPTXTableStyle <#class-pptxtablestyle>`__: Used to layout tables.
     + `class PPTXFillStyle <#class-pptxfillstyle>`__
  * `Working with templates <#working-with-templates>`__
     + `class AbstractTemplate <#class-abstracttemplate>`__: Base class for all custom templates (enforce necessary attributes)
     + `class TemplateExample <#class-templateexample>`__: Example class to show how to work with custom templates
  * `utils.py <#utilspy>`__: A collection of useful functions, eg. to generate PDF or PNG from \*.pptx (needs PowerPoint installed)
  * `Examples <#example>`__: Collection of examples demonstrating how to use python-pptx-interface.
     + `Example <#example>`__: demonstrates usage of some key-features of python-pptx-interface with explanations
     + `General example 01 <#general-example-01>`__: demonstrates usage of some key-features of python-pptx-interface
     + `Font style example 01 <#font-style-example-01>`__
     + `Table style example 01 <#table-style-example-01>`__


class PPTXCreator
-----------------

This class provides an easy interface to create a PowerPoint presentation via python-pptx. It has methods to add slides
and shapes (tables, textboxes, matplotlib figures) setting format by using layouts and stylesheets. It also has methods
to move slides around, remove empty placeholders or create hyperlinks.

**Methods defined:**

* add_content_slide
    Add a content slide with hyperlinks to all other slides and puts it to position slide_index.
* add_latex_formula
    Add the given latex-like math-formula as an image to the presentation using matplotlib.
* add_matplotlib_figure
    Add a motplotlib figure to slide and position it via position.
    Optional parameter zoom sets image scaling in PowerPoint. Only used if width not in kwargs (default = 1.0).
* add_slide
    Add a new slide to presentation. If no layout is given, default_layout is used.
* add_table
    Add a table shape with given table_data at position using table_style. (table_data: outer iter -> rows, inner iter cols; auto_merge: not implemented jet)
* add_text_box
    Add a text box with given text using given position and font. Uses self.default_position if no position is given.
* add_title_slide
    Add a new slide to presentation. If no layout is given, title_layout is used.
* move_slide
    Move the given slide to position new_index.
* save
    Save presentation under the given filename.
* save_as_pdf
    Save the presentation as pdf under the given filenmae. Needs PowerPoint installed.
* save_as_png
   Saves the presentation as PNG's in the given folder. Needs PowerPoint installed.

**Static methods defined:**

* create_hyperlink(run: pptx.text.text._Run, shape: pptx.shapes.autoshape.Shape, to_slide: pptx.slide.Slide)
    Make the given run a hyperlink to to_slide.
* remove_unpopulated_shapes(slide: pptx.slide.Slide)
    Removes empty placeholders (e.g. due to layout) from slide. Further testing needed.

**Properties defined:**

* **prs**: python-pptx Presentation object
* **slides**: list of all slides in presentation
* **template**: used template file
* **title_layout**: laxout used for title slide
* **default_layout**: default layout
* **default_position**: used, when no PPTXPosition is given to add_table/add_text_box/... methods


class PPTXPosition
------------------

To position shapes in a slide, many methods of PPTXCreator except a PPTXPosition parameter. It allows to give a position
relative to slide width and high (as a fraction). Additionally you can specify the position in inches starting from the
relative position. Some stylesheets e.g. PPTXTableStyle can also have an optional PPTXPosition attribute. In that case
writing the style to a shape will also set its position.

**Methods defined:**

* dict
    Returns a kwargs dict containing "left" and "top".
* tuple
    Returns an args tuple containing "left" and "top".

**Properties defined:**

* **left**: left position [inches] starting from rel_left
* **left_rel**: distance from slide left (relative to slide width)
* **prs**: python-pptx Presentation object
* **top**: top position [inches] starting from rel_top
* **top_rel**: distance from slide top (relative to slide height)

Stylesheets
-----------
While python-pptx-interface can load a template file with placeholders, the intended use case is more focused on
creating and positioning shapes like tables, pictures, textboxes etc. directly in python. Therefore all unused
placeholders are removed by default, when creating a new slide. As it can be quite tedious to do all the necessary
formatting directly using python-pptx, this package provides some style sheet like classes, to define a certain format
and than "write" it to the created shapes. In general python-pptx-interface styles only change parameters, that
have been set. E.g. when creating a PPTXFontStyle instance and setting the font size, using this style will only
change the font size, but not color, bold ... attributes. Beside setting an attribute or not changing an attribute
there is a third case - using the default value as it is defined e.g. in the master slide. For that case, the value
**_USE_DEFAULT** can be used.

**To be consistent, python-pptx-interface will not change anything if an attribute is set to None.
This can differ from the pyrhon-pptx behaviour in some cases, where None means "use default".**

class PPTXFontStyle
~~~~~~~~~~~~~~~~~~~
`font-style example <https://github.com/natter1/python_pptx_interface/blob/master/pptx_tools/examples/font_style_example_01.py>`_

**Methods defined:**

* read_font
    Read attributes from a pptx.text.text.Font object.
* set
    Convenience method to set several font attributes together.
* write_font
    Write attributes to a pptx.text.text.Font object.
* write_paragraph
    Write attributes to given paragraph.
* write_run
    Write attributes to given run.
* write_shape
    Write attributes to all paragraphs in given pptx.shapes.autoshape.Shape.
    Raises TypeError if given shape has no text_frame or table.
* write_text_frame
    Write attributes to all paragraphs in given text_frame.

**Properties defined:**

* **bold**
* **caps**
* **color_rgb**
* **fill_style**
* **italic**
* **underline**
* **size**
* **strikethrough**



class PPTXParagraphStyle
~~~~~~~~~~~~~~~~~~~~~~~~

`paragraph-style example <https://github.com/natter1/python_pptx_interface/blob/master/pptx_tools/examples/paragraph_style_example_01.py>`_,

**Methods defined:**

* read_paragraph
    Read attributes from a _Paragraph object.
* set
    Convenience method to set several paragraph attributes together.
* write_paragraph
    Write attributes to given paragraph.
* write_shape
    Write attributes to all paragraphs in given pptx.shapes.autoshape.Shape.
    Raises TypeError if given shape has no text_frame or table.
* write_text_frame
    Write attributes to all paragraphs in given text_frame.

**Properties defined:**

* **alignment**
* **font_style**
* **level**
* **line_spacing**
* **space_after**
* **space_before**


class PPTXTableStyle
~~~~~~~~~~~~~~~~~~~~
`table-style example <https://github.com/natter1/python_pptx_interface/blob/master/pptx_tools/examples/table_style_example_01.py>`_,

**Methods defined:**

* read_table
    Read attributes from a Table object, ignoring font and cell style.
* set
    Convenience method to set several table attributes together.
* set_width_as_fraction
    Set table width as fraction of slide width.
* write_shape
    """Write attributes to table in given pptx.shapes.autoshape.Shape."""
    Raises TypeError if given shape has no text_frame or table.
* write_table
    """Write attributes to table object."""

**Properties defined:**

* **cell_style**
* **col_banding**
* **col_ratios**
* **first_row_header**
* **font_style**
* **line_spacing**
* **position**
* **row_banding**
* **space_after**
* **space_before**
* **width**


class PPTXFillStyle
~~~~~~~~~~~~~~~~~~~

...

**Methods defined:**

* set
    Convenience method to set several fill attributes together.
* write_fill
    Write attributes to a FillFormat object.

**Properties defined:**

* **fill_type**
* **fore_color_rgb**
* **fore_color_mso_theme**
* **fore_color_brightness**
* **back_color_rgb**
* **back_color_mso_theme**
* **back_color_brightness**
* **pattern**


Working with templates
----------------------

...

class AbstractTemplate
~~~~~~~~~~~~~~~~~~~~~~

...

class TemplateExample
~~~~~~~~~~~~~~~~~~~~~

...

utils.py
~~~~~~~~

...


Examples
--------

Example
~~~~~~~

.. figure:: https://github.com/natter1/python_pptx_interface/raw/master/docs/images/example01_title_slide.png
    :width: 500pt

|

This module comes with a
`general example <https://github.com/natter1/python_pptx_interface/blob/master/pptx_tools/examples/general_example_01.py>`_,
that you could run like

.. code:: python

    from pptx_tools.examples import general_example_01 as example

    example.run(save_dir=my_dir)  # you have to specify the folder where to save the presentation

This will create an example.pptx, using some of the key-features of python-pptx-interface. Lets have a closer look:

|

.. code:: python

    from pptx_tools.creator import PPTXCreator, PPTXPosition
    from pptx_tools.style_sheets import font_title, font_default
    from pptx_tools.templates import TemplateExample

    from pptx.enum.lang import MSO_LANGUAGE_ID
    from pptx.enum.text import MSO_TEXT_UNDERLINE_TYPE

    try:
        import matplotlib.pyplot as plt
        matplotlib_installed = True
    except ImportError as e:
        matplotlib_installed = False

First we need to import some stuff. **PPTXCreator** is the class used to create a \*.pptx file.
**PPTXPosition** allows as to position shapes in more intuitive units of slide width/height.
**font_title** is a function returning a PPTXFontStyle instance. We will use it to change the formatting of the title shape.
**TemplateExample** is a class providing access to the example-template.pptx included in python-pptx-interface
and also setting some texts on the master slides like author, date and website. You could use it as reference
on how to use your own template files by subclassing AbstractTemplate
(you need at least to specify a path to your template and define a default_layout and a title_layout).

**MSO_LANGUAGE_ID** is used to set the language of text and **MSO_TEXT_UNDERLINE_TYPE** is used to format underlining.

Importing matplotlib is optional - it is used to demonstrate, how to get a matplotlib figure into your presentation.

|
|

.. code:: python

    def run(save_dir: str):
        pp = PPTXCreator(TemplateExample())

        PPTXFontStyle.lanaguage_id = MSO_LANGUAGE_ID.ENGLISH_UK
        PPTXFontStyle.name = "Roboto"

        title_slide = pp.add_title_slide("Example presentation")
        font = font_title()  # returns a PPTXFontStyle instance with bold font and size = 32 Pt
        font.write_shape(title_slide.shapes.title)  # change font attributes for all paragraphs in shape

Now we create our presentation with **PPTXCreator** using the **TemplateExample**.
We also set the default font language and name of all **PPTXFontStyle** instances. This is not necessary,
as *ENGLISH_UK* and *Roboto* are the defaults anyway. But in principle you could change these settings here,
to fit your needs. If you create your own template class, you might also set these default parameters there.
Finally we add a title slide and change the font style of the title using title_font().

|
|

.. code:: python

        slide2 = pp.add_slide("page2")
        pp.add_slide("page3")
        pp.add_slide("page4")
        content_slide = pp.add_content_slide()  # add slide with hyperlinks to all other slides

Next, we add three slides, and create a content slide with hyperlinks to all other slides. By default,
it is put to the second position (you could specify the position using the optional slide_index parameter).

.. figure:: https://github.com/natter1/python_pptx_interface/raw/master/docs/images/example01_content_slide.png
    :width: 500pt

|
|

Lets add some more stuff to the title slide.

.. code:: python

    text = "This text has three paragraphs. This is the first.\n" \
           "Das ist der zweite ...\n" \
           "... and the third."
    my_font = font_default()  # font size 14
    my_font.size = 16
    text_shape = pp.add_text_box(title_slide, text, PPTXPosition(0.02, 0.24), my_font)

**PPTXCreator.add_text_box()** places a new text shape on a slide with the given text.
Optionally it accepts a PPTXPosition and a PPTXFont. With PPTXPosition(0.02, 0.24)
we position the figure 0.02 slide widths from left and 0.24 slide heights from top.

|
|

.. code:: python

    my_font.set(size=22, bold=True, language_id=MSO_LANGUAGE_ID.GERMAN)
    my_font.write_paragraph(text_shape.text_frame.paragraphs[1])

    my_font.set(size=18, bold=False, italic=True, name="Vivaldi",
                language_id=MSO_LANGUAGE_ID.ENGLISH_UK,
                underline=MSO_TEXT_UNDERLINE_TYPE.WAVY_DOUBLE_LINE)
    my_font.write_paragraph(text_shape.text_frame.paragraphs[2])

We can use my_font to format individual paragraphs in a text_frame with **PPTXFontStyle.write_paragraph()**.
Via **PPTXFontStyle.set()** easily customize the font before using it.

|
|

.. code:: python

        table_data = []
        table_data.append([1, 2])  # rows can have different length
        table_data.append([4, slide2, 6])  # there is specific type needed for entries (implemented as text=f"{entry}")
        table_data.append(["", 8, 9])

        pp.add_table(slide2, table_data)

we can also easily add a table. First we define all the data we want to put in the table. Here we use a list of lists.
But add_table is more flexible and can work with anything, that is an Iterable of Iterable. The outer iterable defines,
how many rows the table will have. The longest inner iterable is used to get the number of columns.

|
|

.. code:: python

        if matplotlib_installed:
            fig = create_demo_figure()
            pp.add_matplotlib_figure(fig, title_slide, PPTXPosition(0.3, 0.4))
            pp.add_matplotlib_figure(fig, title_slide, PPTXPosition(0.3, 0.4, fig.get_figwidth(), -1.0), zoom=0.4)
            pp.add_matplotlib_figure(fig, title_slide, PPTXPosition(0.3, 0.4, fig.get_figwidth(), 0.0), zoom=0.5)
            pp.add_matplotlib_figure(fig, title_slide, PPTXPosition(0.3, 0.4, fig.get_figwidth(), 1.5), zoom=0.6)


If matplotlib is installed, we use it to create a demo figure, and add it to the title_slide.
With PPTXPosition(0.3, 0.4) we position the figure 0.3 slide widths from left and 0.4 slide heights from top.
PPTXPosition has two more optional parameters, to further position with inches values (starting from the relative position).

|
|

.. code:: python

        pp.save(os.path.join(save_dir, "example.pptx"))

Finally, we save the example as example.pptx.

|
|

If you are on windows an have PowerPoint installed, you could use some additional features.

.. code:: python

    try:  # only on Windows with PowerPoint installed:
        filename_pptx = os.path.join(save_dir, "example.pptx")
        filename_pdf = os.path.join(save_dir, "example.pdf")
        foldername_png = os.path.join(save_dir, "example_pngs")

        # use absolute path, because its not clear where PowerPoint saves PDF/PNG ... otherwise
        pp.save(filename_pptx, create_pdf=True)  # saves your pptx-file and also creates a PDF file
        pp.save_as_pdf(filename_pdf, overwrite=True)  # saves presentation as PDF
        pp.save_as_png(foldername_png, overwrite_folder=True)  # creates folder with PNGs of slides
    except Exception as e:
        print(e)

General example 01
~~~~~~~~~~~~~~~~~~
`general example 01 <https://github.com/natter1/python_pptx_interface/blob/master/pptx_tools/examples/general_example_01.py>`_

Font style example 01
~~~~~~~~~~~~~~~~~~~~~
`font style example 01 <https://github.com/natter1/python_pptx_interface/blob/master/pptx_tools/examples/font_style_example_01.py>`_

Table style example 01
~~~~~~~~~~~~~~~~~~~~~~
`table style example 01 <https://github.com/natter1/python_pptx_interface/blob/master/pptx_tools/examples/table_style_example_01.py>`_

Requirements
------------
* Python >= 3.6 (f-strings)
* python-pptx

Optional requirements
---------------------
* matplotlib (adding matplotlib figures to presentation)
* comtypes  (create PDF's or PNG's)
* PowerPoint (create PDF's or PNG's)

Contribution
------------
Help with this project is welcome. You could report bugs or ask for improvements by creating a new issue.

If you want to contribute code, here are some additional notes:

* This project uses 120 characters per line.
* Try to avoid abbreviations in names for functions or variables.
* Use type hints.
* Use Slide objects instead of IDs or index values as function parameter.
