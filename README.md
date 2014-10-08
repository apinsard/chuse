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

Chuse is available from the [sapher overlay][1].

Once you have added this overlay through layman or whatever, just emerge `chuse`.

    # emerge -av chuse

By default, `chuse` uses the following hierarchy pattern:

    /etc/portage/package.use/<cat-name>/<pkg-name>

This file would contain each rules for the package <pkg-name> in the category <cat-name>.

### If you don't use this hierarchy pattern

#### If you want to use your own current pattern

You can toggle this default pattern by explicitely setting the PACKAGE\_USE\_FILE\_PATTERN
environment variable (eg. in your bashrc).

PACKAGE\_USE\_FILE\_PATTERN examples:

    PACKAGE\_USE\_FILE\_PATTERN="/etc/portage/package.use" # All in one file
    PACKAGE\_USE\_FILE\_PATTERN="/etc/portage/package.use/%(cat)s" # One file per category
    PACKAGE\_USE\_FILE\_PATTERN="/etc/portage/package.use/%(cat)s/%(pkg)s" # This is the default
    PACKAGE\_USE\_FILE\_PATTERN="/etc/portage/package.use/chuse" # One file dedicated to chuse

`%(cat)s` and `%(pkg)s` would be respectively replaced by the category name and the name of the
package for which you want to alter USE flags.

#### If you want to adopt this hierarchy pattern

You will have to backup your current USE flags in a file and reorganize the package.use hierarchy.
To achieve this, issue the following commands:

    # chuse --dump > /root/package.use.bak # Concat all your rules in one file
    # rm -r /etc/portage/package.use
    # chuse --load /root/package.use.bak # Parse the file to organize your package.use

To avoid wrong usage, `chuse --load` will fail if /etc/portage/package.use exists.

Usage
-----

    chuse <atom> [[modifier]<flag> ...] [(--because|#) <reason>]

    atom:
        [version-selector]<ebuild-cat>/<ebuild-pkg>[-<version>][:<slot>]
        Obviously 'version-selector' and 'version' should never be set without the other.
    version-selector:
        One of : "=", ">", "<", ">=", "<=".
    ebuild-cat:
        An ebuild category (validity won't be checked).
    ebuild-pkg:
        An ebuild package (validity won't be checked).
    version:
        A version of the underlying package (validity won't be checked).
    slot:
        A slot of the underlying package (validity won't be checked.
    modifier:
        One of : "+", "-", "%" (% means reset default). If omitted, "+" is assumed.
    flag:
        A USE flag (validity won't be checked).
    reason:
        The reason why you changed these USE flags (so that you remember why you set this and if now
        you can reset default for instance).

[1]: https://github.com/apinsard/sapher-overlay
