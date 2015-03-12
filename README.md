cvs2gitdump
===========

A small python script which imports cvs tree into git repository.

Pros:
- Small footprint
- Supports incremental import.  It's very fast
- Convert tags on HEAD
- Everythings is done on memory

Cons:
- Doesn't convert any branches

An alternative to
- [git-cvs](https://github.com/ustuehler/git-cvs)
- [bigcvs2git](https://github.com/jcs/bigcvs2git)
- [cvs2svn](http://cvs2svn.tigris.org/)

Prerequirement:
- [rcsparse](http://gitorious.org/fromcvs/rcsparse)


Usage
-----

    usage: cvs2gitdump [-ah] [-z fuzz] [-e email_domain] [-E log_encodings]
        [-k rcs_keywords] [-b branch] [-m module] [-l last_revision]
	cvsroot [git_dir]


### Options

* -a

  As the default the script will only use commits 10 minutes older than
  the most recent commit because recent commits may not stable if the
  repository is changing.  This option will change this behavior, it
  will use the entire commits.

* -b branch

  The branch name of the git repository which is used for incremental
  import.

* -h

  Show the usage.

* -z fuzz

  When the script collects changesets from CVS repository, commits by
  the same author, using the same log message and within ``fuzz``
  seconds are collected into the same changeset.  300 (seconds) is used
  as the default.

* -e email_domain

  Append the email domain to the author.

* -E log_encodings

  Specify the character encodings used for decoding CVS logs.  Multiple
  encodings can be specified by spearating with ','.   Specified encodings
  are used in order for decoding the log.  Default is 'utf-8,iso-8859-1'

* -k rcs_keywords

  Add an extra RCS keyword which are used by CVS.  The script
  substitutes the RCS keyword by the same way as $Id$.

* -m module

  Specify the target module name in the target cvsroot.  The script will
  dump only the directory specified by this option.

* -l last_rev

  Specify the last revision which is used for finding the last change
  set in the CVS tree.  Specify in SHA-1.

* cvsroot

  The target cvsroot or the sub directory of the cvsroot.  The script treats
  this directory as the root directory.

* git_dir

  The git repository.  Specify this for incremental import.

Example
-------

First import:

    % git init --bare /git/openbsd.git
    % python cvs2gitdump.py -k OpenBSD -e openbsd.org /cvs/openbsd/src > openbsd.dump
    % git --git-dir /git/openbsd.git fast-import < openbsd.dump

Periodic import:

    % sudo cvsync
    % python cvs2gitdump.py -k OpenBSD -e openbsd.org /cvs/openbsd/src /git/openbsd.git > openbsd2.dump
    % git --git-dir /git/openbsd.git fast-import < openbsd2.dump


cvs2svndump
===========

A small python script which imports cvs tree into subversion repository.

Pros:
- Small footprint
- Supports incremental import is supported.  It's very fast
- Everythings is done on memory

Cons:
- Doesn't convert tags and branches

Prerequirement:
- [rcsparse](http://gitorious.org/fromcvs/rcsparse)
- svn (Python interface for subversion)


Usage
-----

    usage: cvs2svndump [-ah] [-z fuzz] [-e email_domain] [-E log_encodings]
	[-k rcs_keywords] [-m module] cvsroot [svnroot svnpath]]


### Options

* -a

  As the default the script will only use commits 10 minutes older than
  the most recent commit because recent commits may not stable if the
  repository is changing.  This option will change this behavior, it
  will use the entire commits.

* -h

  Show the usage.

* -z fuzz

  When the script collects changesets from CVS repository, commits by
  the same author, using the same log message and within ``fuzz``
  seconds are collected into the same changeset.  300 (seconds) is used
  as the default.

* -e email_domain

  Append the email domain to the author.

* -E log_encodings

  Specify the character encodings used for decoding CVS logs.  Multiple
  encodings can be specified by spearating with ',' and they are used in
  order.  Default is 'utf-8,iso-8859-1'

* -k rcs_keywords

  Add an extra RCS keyword which are used by CVS.  The script
  substitutes the RCS keyword by the same way as $Id$.

* -m module

  Specify the target module name in the target cvsroot.  The script will
  dump only the directory specified by this option.

* cvsroot

  The target cvsroot or the sub directory of the cvsroot.  The script treats
  this directory as the root directory.

* svn_dir svn_path

  Specify the svn repository and path.  Specify these for incremental
  import.  When the script searches the last commit, it excepts the commits
  whose author are 'svnadmin'.  Use 'svnadmin' for manually fixing.


Example
-------

First import:

    % python cvs2svndump.py -k OpenBSD /cvs/openbsd/src > openbsd.dump
    % svnadmin create /svnrepo
    % svn mkdir --parents -m 'mkdir /vendor/openbsd/head/src' file:///svnrepo/vendor/openbsd/head/src
    % svnadmin load --parent-dir /vendor/openbsd/head/src /svnrepo < openbsd.dump

Periodic import:

    % sudo cvsync
    % python cvs2svndump.py -k OpenBSD /cvs/openbsd/src file:///svnrepo vendor/openbsd/head/src > openbsd2.dump
    % svnadmin load /svnrepo < openbsd2.dump

