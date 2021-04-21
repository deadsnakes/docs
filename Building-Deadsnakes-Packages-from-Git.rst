Launchpad PPAs only keep the newest version of a package (per Ubuntu release); older
versions are simply deleted. If you need an older version of a deadsnakes package,
you can build it from the source repository.

The following instructions assume you started from a regular installation of the
distribution release that you're building for. You can cross-build packages using
pbuilder, but that's out of the scope of these instructions.


Build Dependencies
------------------

If you add the appropriate deadsnakes PPA to the system and then run ``apt-get update``,
you can simply run ``apt-get build-dep <packagename>`` to install the build dependencies
for a package. Otherwise, the build commands will complain if you're missing a build
dependency; simply install the missing packages manually.


Getting the Source
------------------

1. Install the following packages::

       sudo apt-get install devscripts git git-buildpackage

2. Find the repository for the package you want to build (`list`_) and clone it::

       git clone https://github.com/deadsnakes/python<X.Y>.git
       cd python<X.Y>

   .. _list: https://github.com/deadsnakes/

3. Find the tag that corresponds to the exact package version you want to build.
   Always pick the package for the distribution you're targeting; every package version
   contains the code name of the distribution it's for. Check out the tagged revision::

    git checkout <tagname>

The form of the tag name determines the commands to use for building:

- if the tag has the form *debian/<pkgversion>*: continue to the `Building`_ section.
- otherwise, if the tag has the form *python<X.Y>_<pkgversion>*: see the `Legacy Building`_ section.


Building
--------

1. Run::

    gbp buildpackage --git-ignore-branch

   On older distributions, the ``gbp`` command may not exist. In that case, run::

    git buildpackage --git-ignore-branch

   The command may ultimately fail with an error because it can't find the correct private
   GPG key to sign the source package. The binary packages should still have been
   created and the error can be ignored.

2. The binary packages can be found in the parent directory.


Legacy Building
---------------

To build from an old version imported into a Git repository, you need to create an upstream tarball.

1. Find the matching upstream tag; this will be of the form *python<X.Y>_<X.Y.Z>*. Check out that tag::

    git checkout python<X.Y>_<X.Y.Z>

2. Run::

    tar czf ../python<X.Y>_<X.Y.Z>.orig.tar.gz .

3. Check out to the package tag you determined before.
4. Run::

    debuild

   The binary packages will be created in the parent directory.
