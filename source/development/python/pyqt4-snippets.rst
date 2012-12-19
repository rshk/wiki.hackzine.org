Python: PyQt4 Snippets
####

Have a look at the `PyQt4 classes reference`_.

QTreeView example usage
====

.. code-block:: python

    model = QtGui.QStandardItemModel(5, 2)
    for r in range(5):
        for c in range(2):
            item = QtGui.QStandardItem("Row: %d, Column: %d" % (r, c))
            if c == 0:
                for i in range(3):
                    child = QtGui.QStandardItem("Item %d" % i)
                    child.setEditable(False)
                    item.appendRow(child)
            model.setItem(r, c, item)

    model.setHorizontalHeaderItem(0, QtGui.QStandardItem("Foo"));
    model.setHorizontalHeaderItem(1, QtGui.QStandardItem("Bar-Baz"));
    ui.sideTree.setModel(model)

Lockable main window
====

This is a ``QMainWindow`` which close can be locked, and forced to emit a signal instead of just close the window.
Useful eg. to ask whether to save changes, etc.

.. code-block:: python

    from PyQt4.QtGui import QMainWindow, QMessageBox
    from PyQt4.QtCore import SIGNAL

    class LockableMainWindow(QMainWindow):
        """Custom main window whit close event lockable

        Custom Methods:
            setCloseable(c)
                True : unlock, False : lock

            lockClose()
                alias for setCloseable(False)

            unlockClose()
                alias for setCloseable(True)

        Custom Signals:
            closeRequested()
                Emitted when trying to close a locked window
        """

        CAN_BE_CLOSED = True

        def setCloseable(self, c=True):
            self.CAN_BE_CLOSED = bool(c)

        def lockClose(self):
            self.setCloseable(False)

        def unlockClose(self):
            self.setCloseable(True)

        def closeEvent(self, event):
            """Handles the close event.
            If window close is locked, ignore the close event and emit
            ``closeRequested()`` signal.
            """
            if not self.CAN_BE_CLOSED:
                event.ignore()
                self.emit(SIGNAL("closeRequested()"))

.. _PyQt4 classes reference: http://www.riverbankcomputing.co.uk/static/Docs/PyQt4/html/classes.html
