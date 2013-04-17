####################################
Javascript: triggering a click event
####################################

Let's assume we have this link, and we want to programmatically trigger a click on it.

.. code-block:: html

    <a href="javascript:void(0)" id="my-link">This is my link</a>

Usually, this should work:

.. code-block:: javascript

    document.getElementById('my-link').click();

but for some reason, that's not always the case.

To solve the problem, I used this code, that generates a "mouse click" event and dispatches to the link/button.

.. code-block:: javascript

    var evObj = document.createEvent('MouseEvents');
    evObj.initEvent('click', true, false);
    document.getElementById('my-link').dispatchEvent(evObj);
