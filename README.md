Chuse
=====

Chuse stands for CHange USE. This is a Portage tool for managing your local USE flags (ie. set
within `/etc/portage/package.use`).

In contrast to its several equivalents, it is designed with a very specific goal: Make your
`/etc/portage/package.use` management easier. This means `chuse` does not handle other `package.*`
and **focuses** on `package.use`. Indeed, I think you often have to deal with `package.use` and need
a clean structure. On the other hand, you rarely have more than 10 lines in your `package.mask`,
`package.keywords`... and open underlying files to read or edit them just work fine.

Installation
------------

Chuse is available from [flora overlay][1]. It is also available in the main Funtoo tree.

Once you have added this overlay, just emerge `chuse`.

    # emerge -av chuse

By default, `chuse` uses the following hierarchy pattern:

    /etc/portage/package.use/<cat-name>/<pkg-name>

This file would contain each rules for the package <pkg-name> in the category <cat-name>.

### If you don't use this hierarchy pattern

#### If you want to use your own current pattern

You can toggle this default pattern by explicitely setting the PACKAGE\_USE\_FILE\_PATTERN
environment variable (eg. in your bashrc).

PACKAGE\_USE\_FILE\_PATTERN examples:

    PACKAGE_USE_FILE_PATTERN="/etc/portage/package.use" # All in one file
    PACKAGE_USE_FILE_PATTERN="/etc/portage/package.use/{cat}" # One file per category
    PACKAGE_USE_FILE_PATTERN="/etc/portage/package.use/{cat}/{pkg}" # This is the default
    PACKAGE_USE_FILE_PATTERN="/etc/portage/package.use/chuse" # One file dedicated to chuse

`{cat}` and `{pkg}` will be respectively replaced by the category name and the package name of
the atom you want to alter USE flags.

You can also define the package.use file pattern in `/etc/chuse.conf`, which might be more
convenient for permanent usage.

`/etc/chuse.conf` file example:

    [package.use]
      file-pattern = /etc/portage/package.use/{cat}

#### If you want to adopt this hierarchy pattern

You will have to backup your current USE flags in a file and reorganize the package.use hierarchy.
To achieve this, issue the following commands:

    # chuse --dump > /root/package.use.bak # Concat all your rules in one file
    # chuse --load /root/package.use.bak # Export the backup to the new hierarchy

To avoid mistakes, `chuse --load` will ask you before erasing `/etc/portage/package.use`.

Usage
-----

    chuse [-f|--force] <atom> [[modifier]<flag> ...] [--because <reason>]
        Alter use flags. By default, if the given atom doesn't match any existing ebuild,
        or if one of the given useflags doesn't seem to exist, an error will be raised.
        Use -f/--force option to disable this behavior.  If no flag is specified, the
        current rules matching the underlying atom will be displayed.
    chuse <atom>
        Print current flags set and history.
    chuse (-h|--help)
        Print this help.chuse (-v|--version)
        Print version information.
    chuse --dump
        Print all contents of /etc/portage/package.use
    chuse --load <file>
        Load a backup file to the /etc/portage/package.use hierarchy.

    atom:
        A valid package atom.
    modifier:
        One of: +, -, %. (% means reset default). If omitted, + is assumed.
    flag:
        A USE flag (validity won't be checked).
    reason:
        The reason why you changed these US flags (so that you remember why you set this
        and if you can reset default in the future for instance.

Examples
--------

Add system-cairo, remove system-icu and reset system-jpeg for firefox >= 37.0.1:

    $ sudo chuse ">=www-client/firefox-37.0.1" +system-cairo -system-icu %system-jpeg

Add offensive for sudo:

    $ sudo chuse app-admin/sudo offensive --because much cooler

Show the flags you altered for sudo:

    $ chuse app-admin/sudo
    # [2015-04-18T11:54] Unset sendmail
    # [2015-04-18T11:56] Set offensive
    # much cooler
    app-admin/sudo offensive -sendmail

You can omit the package category:

    $ sudo chuse qtile +dbus
    $ chuse '=qtile-0.10.6'
    # [2017-01-12T07:34] Set dbus
    x11-wm/qtile dbus

Obviously this won't work if the package name is ambiguous:

    $ chuse screen
    Error: This atom is ambiguous, please specify the package category

As well, the existence of ebuilds matching the atom is checked. You can use
the --force (shortly -f) option to disable this behavior:

    $ sudo chuse unexisting/package +foo -bar
    Error: This atom does not match any existing ebuild. Use --force option
    to proceed anyway.
    $ sudo chuse -f unexisting/package +foo -bar

As of version 1.2, useflags existence is also checked:

    $ sudo chuse dev-db/postgresql python -fanfan_la_tulipe dartagnan
    Error: No ebuild matched by the given atom accepts such use flags:
    dartagnan, fanfan_la_tulipe. Make sure you did not make a typo, or use
    --force option to proceed anyway.
    $ sudo chuse -f dev-db/postgresql python -fanfan_la_tulipe dartagnan


[1]: https://github.com/funtoo/flora
