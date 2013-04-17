###################
ANSI escape codes
###################

Colors
======

TODO

Cursor movement
===============

TODO

Extra colors
============

.. code-block:: bash

    #!/bin/bash
    for t in {0..5}; do
        for r in {0..5}; do
            for c in {0..5}; do
                echo -en "\e[48;5;$[ 16 + ( $c * 36 ) + ( $r * 6 ) + $t ]m  \e[0m";
            done;
            echo -n "  ";
        done;
        echo;
    done;
    echo
    for t in {0..5}; do
        for r in {0..5}; do
            for c in {0..5}; do
                echo -en "\e[48;5;$[ 16 + ( $t * 36 ) + ( $r * 6 ) + $c ]m  \e[0m";
            done;
            echo -n "  ";
        done;
        echo;
    done;
    echo
    for t in {0..5}; do
        for r in {0..5}; do
            for c in {0..5}; do
                echo -en "\e[48;5;$[ 16 + ( $c * 36 ) + ( $t * 6 ) + $r ]m  \e[0m";
            done;
            echo -n "  ";
        done;
        echo;
    done;
    echo
    for t in {0..5}; do
        for r in {0..5}; do
            for c in {0..5}; do
                echo -en "\e[48;5;$[ 16 + ( $t * 36 ) + ( $c * 6 ) + $r ]m  \e[0m";
            done;
            echo -n "  ";
        done;
        echo;
    done;
    echo
    for t in {0..5}; do
        for r in {0..5}; do
            for c in {0..5}; do
                echo -en "\e[48;5;$[ 16 + ( $r * 36 ) + ( $c * 6 ) + $t ]m  \e[0m";
            done;
            echo -n "  ";
        done;
        echo;
    done;
    echo
    for t in {0..5}; do
        for r in {0..5}; do
            for c in {0..5}; do
                echo -en "\e[48;5;$[ 16 + ( $r * 36 ) + ( $t * 6 ) + $c ]m  \e[0m";
            done;
            echo -n "  ";
        done;
        echo;
    done;
    echo
    for i in {0..15}; do
        echo -en "\e[48;5;${i}m  \e[0m";
    done;
    echo -en "\e[0m  "
    for i in {232..255}; do
        echo -en "\e[48;5;${i}m  \e[0m";
    done;
    echo -e "\e[0m"
