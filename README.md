hey_dummy
=========
Hey Dummy!, your long-running process has finished/failed!

``hey_dummy`` is a way to run long running processes but bring your attention back to them when they finish. Start a job with ``hey_dummy`` then go back to your RSS reader. ``hey_dummy`` will alert you when the job has finished or failed, and you can go back to whatever it was that job was blocking.

Usage
-----

    hey_dummy - Alert yourself that a long-running process has completed
    ---
    -h - display this help
    -d - dry run - don't execute the program, just simulate what would happen if you did
    -a - summary/alert text (defaults to "Hey Dummy!")
    -n - the notifier app (defaults to `notify-send`)
    -p - process to attach to
    -s - summary flag
    -b - body flag
    For examples or more information, go here:
    https://github.com/charlesthomas/hey_dummy/blob/master/README.md

Examples
-----
# Vanilla Use

	hey_dummy long_running_process

This will start ``long_running_process``, and then alert using ``notify-send`` when it completes, with the message:

	Hey Dummy! long_running_process has finished

# Failure Message

	hey_dummy false

This will alert with the message:

	Hey Dummy! false FAILED

# Alert on a Pre-existing Process

	hey_dummy -p $PID

This will run and check that ``$PID`` is still running every second, until the process goes away, and then alert using ``notify-send``. This will **not** be able to tell you the exit status of the process. The message will say (whether the process exited with 0 or anything else):

	Hey Dummy! Process #$PID finished

You probably want to background ``hey_dummy`` with ``&`` if you want control of that terminal window back, otherwise ``hey_dummy`` will hold it until you kill it or the process you're waiting on finishes.

	hey_dummy -p $PID &

# Changing "Hey Dummy!" Text
If "Hey Dummy!" is too offensive for you, use the ``-a`` flag:

	hey_dummy -a "Pardon me sir, but" long_running_process

The above will alert with:

	Pardon me sir, but long_running_process finished

# Using ``hey_dummy`` on OS X with ``growl-notify``
``hey_dummy`` uses ``notify-send`` by default, which doesn't exist on OS X. However, OS X *does* have ``growlnotify``, which is basically the same thing. However, in order to use it, you have to pass a flag to have both Summary text and Body text. (Summary and Body are how they are referred to in the ``man`` page for ``notify-send``. Think of it like an email, where Summary is the Subject Line of the email.)

The syntax is a little weird, but you can ``alias`` this.

	hey_dummy -n growlnotify -b "-m" long_running_osx_process

This tells ``hey_dummy`` to use ``growlnotify`` as the notifier, and tells it to use the ``-m`` flag to pass the body of the alert. ***The double quotes around ``-m`` are extremely important.*** Without them, the ``-m`` flag will be passed to ``hey_dummy`` instead of ``growlnotify``. The output will look like this:

	Hey Dummy! long_running_osx_process finished

In the above message, "long_running_osx_process finished" is the body referenced above.

## Useful Alias
If you're using OS X, it is probably be useful to put this ``alias`` in a ``.bashrc`` or ``.bash_profile`` file.

	alias hey_dummy='hey_dummy -n growlnotify -b "-m"'

Now ``hey_dummy`` can be used like this on OS X:

	hey_dummy long_running_osx_process