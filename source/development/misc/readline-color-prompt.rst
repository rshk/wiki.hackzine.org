Color prompts with readline
#####

Readline is known to have issues with color prompts, such as broken cursor
position calculation, long lines self-overwriting, etc.

In the readline info page::

    -- Function: int rl_expand_prompt (char *prompt)
        Expand any special character sequences in PROMPT and set up the
        local Readline prompt redisplay variables.  This function is
        called by `readline()'.  It may also be called to expand the
        primary prompt if the `rl_on_new_line_with_prompt()' function or
        `rl_already_prompted' variable is used.  It returns the number of
        visible characters on the last line of the (possibly multi-line)
        prompt.  Applications may indicate that the prompt contains
        characters that take up no physical screen space when displayed by
        bracketing a sequence of such characters with the special markers
        `RL_PROMPT_START_IGNORE' and `RL_PROMPT_END_IGNORE' (declared in
        `readline.h'.  This may be used to embed terminal-specific escape
        sequences in prompts.

that are defined as such in ``readline.h``:

.. code-block:: c

    /* Definitions available for use by readline clients. */
    #define RL_PROMPT_START_IGNORE  '\001'
    #define RL_PROMPT_END_IGNORE    '\002'

That's why usually colors in shell prompts should be enclosed between "\[" and "\]":

.. code-block:: bash

    PS1="\[\e[1;34m\]\u@\h:\w \$\[\e[0m\] "

While in Python, you can use something like:

.. code-block:: python

    prompt = "\001\033[1;32m\002cmd>\001\033[0m\002 "
    raw_input(prompt)

**See also:** http://stackoverflow.com/a/9468954/148845
