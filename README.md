Chuse
=====

Chuse stands for CHange USE. This is a Portage tool for managing your local USE flags (ie. set
within `/etc/portage/package.use`).

In contrast to its several equivalents, it is designed with a very specific goal: Make your
`/etc/portage/package.use` management easier. This means `chuse` does not handle other `package.\*`
and **focuses** on `package.use`. Indeed, I think you often have to deal with `package.use` and need
a clean structure. On the other hand, you rarely have more than 10 lines in your `package.mask`,
`package.keywords`... and open underlying files to read or edit them just work fine.

Installation
------------

Chuse is available from the [sapher overlay][1].

Usage
-----

[1]: https://github.com/apinsard/sapher-overlay
