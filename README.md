# Source repository for Argus documentation

Argus documentation is in [reST](http://www.sphinx-doc.org/en/stable/rest.html) (`RST`) format and
is using  to render the documentation. It is available on
[ReadTheDocs](http://argus-documentation.readthedocs.org/): in addition to online reading, offline format (`PDF`,
`Epub`) are also available.

The main contents lives are the `master` branch. Tags are used to define the Argus version the documentation applies to.

## Building and Contributing to the Documentation

If you want to build locally the documentation, first install on your machine the ReadTheDocs theme,

```console
$ sudo yum install python-pip python2-sphinx
$ sudo pip install sphinx_rtd_theme
```

The fork/clone this repository and in the `source` directory, enter:

```console
$ make html
```

The resulting documentation can be accessed in directory `build/html/index.html`.

*Note: after you cloned the repository, [Sphinx](http://www.sphinx-doc.org) will complain about a missing
`_static/` directory: just create it. This is caused by the fact that Git, as many DVCS, doesn't track empty
directories (there is no way to include them in the repository). This `_static/` directory is the default
directory used for static (non `reST` content) but is not used by Argus documentation).*

Contribution to this documentation are welcome: use the usual GitHub
[pull request](https://help.github.com/articles/using-pull-requests/) workflow. Be sure to install
[Sphinx](http://www.sphinx-doc.org) and to compile your modifications before submitting a pull request
(enter a `make clean` before rebuilding the documentation to be sure to detect all problems). The compilation
should produce no warning or error (except the one related to the missing `_static/` directory, see above).

If you are not used to `reST` format, read the (excellent [primer guide](http://www.sphinx-doc.org/en/stable/rest.html)
first. In particular, pay attention that in `reST`, like in Python, the structure is based on identation level:
every time you start a new block, you need to ident the same way all lines part of this block.

## CERN Twiki Migration

The contents of this repository was originally migrated from
[CERN Twiki](https://twiki.cern.ch/twiki/bin/view/EGEE/Author[Sphinx](http://www.sphinx-doc.org)izationFramework),
using [pandoc](http://pandoc.org) (v1.15.2). The original Twiki content is available in
`twiki_pages/` directory of the `twiki_migration_src` branch:

- The original pages in Twiki format) are in `twiki_pages/src/` (the files with a `.disabled` extension are those who
failed the initial conversion with `pandoc`, it was fixed later in the production branch).

- The pages converted with [pandoc](http://pandoc.org) to `reST` format are in `twiki_pages/`. The content is the
raw (unreviewed/unfixed) result of the conversion.

## Production Branch Layout

The production branch also contains a `twiki_pages/` directory with the `src/` subdirectory removed. Contents in
this directory are the not yet migrated pages and two set of pages processed specifically during the migration:

- `twiki_pages/outdated/`: pages not migrated because their contents was too much outdated/irrelevant at the time
the migration was done.

- `twiki_pages/migrated`: pages generally merged into other pages (or significantly restructured) during the
migration.

The main content of the production site is `.rst` files outside `twiki_pages`. Note that Twiki pages have been
renamed and reorganized when migrated to the new production site (layout should be pretty obvious).

The main file is `index.rst` and is mainly a table of contents including other parts of the documentation.
One specific TOC (defined by `toctree` directive) in this file is `hidden`: its purpose is to include files
that are part of the documentation, referenced by other pages but not displayed in the TOC.

Each directory in the documentation has also a `index.html` that is acting as a (more detailed) table of contents
for this particular section. This is the file usually included into the main TOC.

Images must be put into the `_images` directory.
