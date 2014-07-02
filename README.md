hey_dummy
=========
Hey Dummy!, your long-running process has finished/failed!

Version 2.x.x
-------------
Version 2.x.x is ***drastically*** different from [Version
1.0.0](https://github.com/charlesthomas/hey_dummy/releases/tag/1.0.0)
The paradigm of Version 1.0.0 was to pass a bunch of command line options to
``hey_dummy`` in order to fire the alert correctly. Version 2.0.0+ is about
passing the needed values to a *completely separate script* in order to make
customizing alerts easier.

$HEY_DUMMY_NOTIFIER
-------------------
As of Version 2.0.0, $HEY_DUMMY_NOTIFIER is a required environment variable
representing the alert script that ``hey_dummy`` should trigger when the
commands run w/it finish. You should export $HEY_DUMMY_NOTIFIER in your
``.bashrc`` or ``.bash_profile`` file as appropriate.

    export HEY_DUMMY_NOTIFIER='/path/to/alert/script'

If $HEY_DUMMY_NOTIFIER is not set when ``hey_dummy`` runs, ``hey_dummy`` will
fail, and the command you passed to it **will not run**. Instead, you'll see:

    HEY_DUMMY_NOTIFIER not set!

What does ``hey_dummy`` actually do now, then?
----------------------------------------------
As before, ``hey_dummy`` runs everything you pass as command line arguments.
However, instead of controlling the alert itself, ``hey_dummy`` passes the
result status of the passed command to the script designated with
$HEY_DUMMY_NOTIFIER. It also passes the entirety of the passed command to the
$HEY_DUMMY_NOTIFIER script (*after* the result code). It also exits with the
same status as the command it was passed.

OS X ``growlnotify`` Example
----------------------------
Prior to Version 2.0.0, using ``growlnotify`` with ``hey_dummy`` was done like
this:

    hey_dummy -n growlnotify -b "-m" long_running_osx_process

Now (2.0.0+) instead, create a script containing the following:

    #!/bin/bash
    growl_path='/usr/local/bin/growlnotify'

    status_code=$1
    command_name=$2

    # alternately:
    # status_code=$1
    # shift
    # whole_command=$@

    if [[ $status_code == 0 ]]; then
        text="$command_name finished"
    else
        text="$command_name FAILED"
    fi

    $GROWL -m "$text" Hey Dummy!

Export path to script:

    export $HEY_DUMMY_NOTIFIER='/path/to/above/script'

And every time you run ``hey_dummy``, it will execute ``/path/to/above/script``.

This is a GIANT change!
-----------------------
Yes, it is, but it should give you much more flexibility in the ways you want to
alert yourself that something is done. For example, I can now make my
[blink(1)](http://thingm.com/products/blink-1/) blink, play a sound, *and* use
``growlnotify`` by creating a custom $HEY_DUMMY_NOTIFIER script.

In case you're curious:

    #!/bin/bash
    AFPLAY='/usr/bin/afplay'
    BLINK='~/bin/blink1-tool'
    GROWL='/usr/local/bin/growlnotify'

    result=$1
    shift

    if [[ $result == 0 ]]; then
        color='--green'
        sfx="~/sfx/work-complete.wav"
        text="$1 finished"
    else
        color='--red'
        sfx="~/sfx/kaboom.wav"
        text="$1 FAILED"
    fi

    $BLINK --quiet $color --glimmer=3 &
    $AFPLAY $sfx &
    $GROWL -m "$text" Hey Dummy!
