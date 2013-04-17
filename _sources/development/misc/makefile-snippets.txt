#################
Makefile snippets
#################

Quick Makefile reference
========================

Special macros
--------------

* ``$@`` The name of the target
* ``$?`` List of dependents more recent than the target
* ``$^`` All the dependencies of the target
* ``$+`` Like ``$^``, but keeping duplicates and in order of apparence
* ``$<`` Only the first dependency; safer than ``$?`` when you have only one dependency that needs to be on the command
* ``$*`` The matched part in wildcard targets. Eg, if the target label is ``%.c:`` and you call it with ``myfile.c``, the value for ``$*`` will be ``myfile``.


Building a bunch of files
=========================

This is an example standard Makefile I use to build UI files from Qt Designer into Python modules.

.. code-block:: makefile

    # Build the UIs

    ui_files = \
        ui_main_window.py \
        ui_my_dialog0.py \
        ui_my_dialog1.py \
        ui_my_dialog2.py
    rc_files = resources_rc.py

    .PHONY : all
    .PHONY : clean clean_ui clean_rc clean_pyc

    all : ui rc

    ui: $(ui_files)
    rc: $(rc_files)

    $(ui_files): ui_%.py : uis/%.ui
        @## Create UI file
        pyuic4 -x $< -o $@
        @## Fix wrong import
        @# sed "s/^import resources_rc$$/from gui import resources_rc/" -i $@

    $(rc_files): %_rc.py : %.qrc
        pyrcc4 -compress 9 $< -o $@

    clean: clean_pyc clean_ui clean_rc

    clean_ui:
        rm -f $(ui_files)

    clean_rc:
        rm -f $(rc_files)

    clean_pyc:
        rm -f *.pyc


Automatic targets list
======================

In this example case, we have an indefinite bunch of ``.aa`` files, to be built in ``.bb`` files. To avoid writing the whole list of output ``.bb`` files, we can use the following method:

.. code-block:: makefile

    # "Build" .aa files into .bb files (just prefixes each line with '>>> ')

    LIBOBJECTS := $(patsubst %.aa,%.bb,$(wildcard src/*.aa))

    all: $(LIBOBJECTS)

    %.bb: %.aa
            @# Build the .aa file into .bb file
            cat $< | sed 's/^/>>> /' > $@

    clean:
            rm $(LIBOBJECTS)



Exporting PNGs from a single SVG file
=====================================

.. code-block:: makefile

    ## Makefile to build the single images out of the big SVG

    INKSCAPE=inkscape -z
    SOURCE_IMAGE=controls.svg
    OUTPUT_IMAGES=pan.png zoom.png

    .PHONY: all controls clean-elements

    all: controls

    clean: clean-controls

    controls: $(OUTPUT_IMAGES)

    clean-controls:
        rm $(OUTPUT_IMAGES)

    pan.png: $(SOURCE_IMAGE)
        $(INKSCAPE) --file=$< --export-png=$@ --export-area=0:350:50:400

    zoom.png: $(SOURCE_IMAGE)
        $(INKSCAPE) --file=$< --export-png=$@ --export-area=50:350:75:400
