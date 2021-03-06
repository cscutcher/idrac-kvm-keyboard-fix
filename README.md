Dell servers with an iDRAC card allow for remote management of the server,
including a handy remote video console with keyboard and mouse support. This
is served to your web browser as a Java app, but it has a couple of small
problems.


# The problem

As [noted by a user](http://en.community.dell.com/support-forums/servers/f/956/p/19194808/19317794.aspx#19317794)
on the Dell forums, newer Linux distros use `evdev` instead of `kbd` for
the keyboard driver, and the keycodes don't match the ones expected by the
KVM app.

**As a result, certain keys won't work, such as SysRq and the arrow keys.**


# The solution

This is a shared library hack to translate evdev keycodes to old style
keycodes. You then use it via `LD_PRELOAD`.


# How to build and install

    make
    make install

This requires no special privileges, as it installs to your homedir
(`~/local/lib/`).  A wrapper executable is installed to a bin directory in your
`$PATH` to intercept calls to javaws and perform the `LD_PRELOAD` hack. You may
need to add one yourself (`~/bin/` is a reasonable suggestion).


# How to use

Nothing extra, it should Just Work the next time you launch a remote DRAC
console. If not, direct your browser to open the `viewer.jnlp` file with the
executable wrapper.


## Using SysRq

If you want to make use of magic SysReq, make sure that it is disabled on
your local workstation, lest you get a bit of a surprise...

`cat /proc/sys/kernel/sysrq` and set it to 0 if it's not already.


## Additional keysym mapping

A large set of keysyms were merged into the mapping in commit c3f916c. If you
find the hack isn't working as expected, you may need to revert to an earlier
commit, possibly 8f11599. The functionality is dependent on both the local
system and the app served up by the iDRAC, so it's hard to provide definite
predictions. Good luck!
