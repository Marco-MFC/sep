SEP
===

Python and C library for Source Extraction and Photometry.

[![PyPI](https://img.shields.io/pypi/v/sep?label=PyPI)](https://pypi.python.org/pypi/sep)
[![PyPI - Downloads](https://img.shields.io/pypi/dm/sep?label=PyPI%20Downloads)](https://pypi.python.org/pypi/sep)
[![Build Status](https://github.com/sep-developers/sep/workflows/CI/badge.svg)](https://github.com/sep-developers/sep/actions)
[![Documentation Status](https://readthedocs.org/projects/sep/badge/?version=latest)](https://sep.readthedocs.io/en/latest/?badge=latest)
[![JOSS](http://joss.theoj.org/papers/10.21105/joss.00058/status.svg)](http://dx.doi.org/10.21105/joss.00058)

*"... [it's] an SEP: Somebody Else's Problem."
"Oh, good. I can relax then."*

SEP, SEP-PJW, and Package Names
-------------------------------

`sep` was originally released by Kyle Barbary, at
[kbarbary/sep](https://github.com/kbarbary/sep) (``sep<=1.2.1``). For a
brief period, the package was maintained by Peter Watson, under the
`sep-pjw` package name, at
[PJ-Watson/sep-pjw](https://github.com/PJ-Watson/sep-pjw) and
[PyPI/sep-pjw](https://pypi.org/project/sep-pjw/)
(``1.3.0<=sep-pjw<=1.3.8``). Both of these repositories will be archived,
and future development will take place at
[sep-developers/sep](<https://github.com/sep-developers/sep>)
(``sep>=1.4.0``). Note that there may be some incompatibilities between
``sep==1.2.1`` and ``sep==1.4.0`` when using the C-API directly (to fix
an indexing bug arising with large arrays) - all changes are documented
[here](https://sep-pjw.readthedocs.io/en/latest/changelogs/changes_to_c_api.html).


About
-----

[Source Extractor](http://www.astromatic.net/software/sextractor)
(Bertin & Arnouts 1996) is a widely used
command-line program for segmentation and analysis of astronomical
images. It reads in FITS format files, performs a configurable series
of tasks, including background estimation, source detection,
deblending and a wide array of source measurements, and finally
outputs a FITS format catalog file.

While Source Extractor is highly useful, the fact that it can only be
used as an executable can limit its applicability or lead to awkward
workflows. There is often a desire to have programmatic access to
perform one or more of the above tasks on in-memory images as part of
a larger custom analysis.

**SEP makes the core algorithms of Source Extractor available as a
library of stand-alone functions and classes.** These operate directly
on in-memory arrays (no FITS files or configuration files).  The code
is derived from the Source Extractor code base (written in C) and aims
to produce results compatible with Source Extractor whenever possible.
SEP consists of a C library with no dependencies outside the standard
library, and a Python module that wraps the C library in a Pythonic
API. The Python wrapper operates on NumPy arrays with NumPy as its
only dependency. See below for language-specfic build and usage
instructions.


Python
------

**Documentation:** http://sep.readthedocs.io

**Requirements:**

- Python 3.9+
- numpy 1.23+

**Install release version:**

SEP can be installed with [pip](https://pip.pypa.io):

```
python -m pip install sep
```

If you get an error about permissions, you are probably using your
system Python. In this case, we recommend using [pip's "user
install"](https://pip.pypa.io/en/latest/user_guide/#user-installs)
option to install sep into your user directory:

```
python -m pip install --user sep
```

Do **not** install sep or other third-party Python packages using
`sudo` unless you are fully aware of the risks.


**Install development version:**

Building the development version (from github) requires Cython.
Build and install in the usual place:

```
python -m pip install --editable .
```

**Run tests:** Tests require the [pytest](http://pytest.org) Python
package. To run the tests, execute `./test.py` in the top-level
directory. Some tests require a FITS reader (either fitsio or astropy)
and will be skipped if neither is present.


C Library
---------

_Note: The build process only works on Linux and OS X._

**CMake:** To build using CMake, enter these commands:

```
cd sep
mkdir -p build
cd build
cmake -DCMAKE_INSTALL_PREFIX=/usr ../
```
and follow next steps from the build folder

**Build:** To build the C library from source:

```
make
```

**Run tests:**

```
make test
```

**Install** The static library and header can be installed with

```
make install
make PREFIX=/path/to/prefix install
```

This will install the shared and static library in `/path/to/prefix/lib`
and header file in `/path/to/prefix/include`. The default prefix is
`/usr/local`.

**API:** The C library API is documented in the header file
[sep.h](src/sep.h).


**Rust bindings:** Low-level Rust wrapper for the C library can be found at https://crates.io/crates/sep-sys

Contributing
------------

- Report a bug or documentation issue: http://github.com/sep-developers/issues
- Ask (or answer) a question: https://github.com/sep-developers/sep/discussions/categories/q-a

Development of SEP takes place on GitHub at
[sep-developers/sep](http://github.com/sep-developers/sep).  Contributions of bug fixes,
documentation improvements and minor feature additions are welcome via
GitHub pull requests. For major features, it is best to discuss the change first
via [GitHub Discussions](https://github.com/sep-developers/sep/discussions/).


Citation
--------

If you use SEP in a publication, please cite the following article in the Journal of Open Source Software:

[![JOSS](http://joss.theoj.org/papers/10.21105/joss.00058/status.svg)](http://dx.doi.org/10.21105/joss.00058)

Please also cite the original Source Extractor paper ([Bertin & Arnouts
1996](http://adsabs.harvard.edu/abs/1996A%26AS..117..393B)).

The DOI for the sep v1.0.0 code release is:

[![DOI](https://zenodo.org/badge/doi/10.5281/zenodo.159035.svg)](http://dx.doi.org/10.5281/zenodo.159035)

License
-------

The license for all parts of the code derived from Source Extractor is
LGPLv3. The license for code derived from photutils (`src/overlap.h`)
is BSD 3-clause. Finally, the license for the Python wrapper
(`sep.pyx`) is MIT. The license for the library as a whole is
therefore LGPLv3. The license for each file is explicitly stated at
the top of the file and the full text of each license can be found in
`licenses`.


FAQ
---

**Why isn't the C library part of Source Extractor?**

Source Extractor is *not* designed as a library with an
executable built on top of the library. In Source Extractor, background
estimation, object detection and photometry are deeply integrated into the
Source Extractor executable. Many changes to the code were necessary in
order to put the functionality in stand-alone C functions. It's too much
to ask of the Source Extractor developer to rewrite large parts of the
core of the Source Extractor program with little gain for the executable.

**What sort of changes?**

- Source Extractor reads in only a small portion of each image at a
  time.  This allows it to keep its memory footprint extremely low and
  to operate on images that are much larger than the system's physical
  memory. It also means that a FITS reader is deeply integrated into
  the code.  SEP operates on images in memory, so all the FITS I/O
  machinery in Source Extractor is not used here.

- Error handling: When it encounters a problem, Source Extractor
  immediately exits with an error message. This is fine for an
  executable, but a library function doesn't have that luxury. Instead
  it must ensure that allocated memory is freed and return an error
  code.

- Options: Source Extractor has many options that affect its behavior. These
  are stored in a global structure used throughout the executable. In SEP,
  options for a particular function are passed as function parameters.

- Array types: Source Extractor can operate on FITS images containing various
  types of data (float, double, int, etc). Internally, it does this by
  converting all data to `float` immediately when reading from disk.
  SEP does something similar, but in memory: SEP functions typically convert
  input arrays to float on the fly within each function, then perform
  all operations as floating point.

**Is SEP as fast as Source Extractor?**

It's fast. It should be similar to Source Extractor as a lot of the code
is identical. Source Extractor has the advantage of doing all the
operations (detection and analysis) simultaneously on each image
section, which may confer CPU cache advantages, but this hasn't been
tested at all. On the other hand, depending on your usage SEP might
let you avoid writing files to disk, which is likely to be a bigger
win.

**What happens when Source Extractor is updated in the future?**

SEP can be considered a fork of the Source Extractor code base: it's
development will not track that of Source Extractor in any automated
way. However, the algorithms implemented so far in SEP are stable in
Source Extractor: the SEP code was forked from v2.18.11, yet it is tested
against the results of v2.8.6. This indicates that the algorithms have
not changed in Source Extractor over the last few years.

**In the Python interface, why do I have to byte swap data when using
astropy.io.fits?**

This occurs because FITS files have big-endian [byte
order](http://en.wikipedia.org/wiki/Endianness), whereas most widely
used CPUs have little-endian byte order. In order for the CPU to
operate on the data, it must be byte swapped at *some point.* Some
FITS readers such as [fitsio](http://github.com/esheldon/fitsio) do
the byte swap immediately when reading the data from disk to memory,
returning numpy arrays in native (little-endian) byte order. However,
astropy.io.fits does not (for reasons having to do with memory
mapping). Most of the time you never notice this because when you do
any numpy operations on such arrays, numpy uses an intermediate buffer
to byte swap the array behind the scenes and returns the result as a
native byte order array. Internally, SEP is not using numpy
operations; it's just getting a pointer to the data in the array and
passing it to C code. As the C code does not include functionality to
do buffered byte swapping, the input array must already be in native
byte order.

It would be possible to add buffered byte swapping capability to the
SEP code, but it would increase the code complexity. A simpler
alternative would be to make a byte swapped copy of the entire input
array, whenever necessary. However, this would significantly increase
memory use, and would have to be done repeatedly in multiple SEP
functions: `Background`, `extract`, `sum_circle`, etc. Each would make
a copy of the entire data array. Given these considerations, it seemed
best to just explicitly tell the user to do the byte swap operation
themselves so they could just do it once, immediately after reading in
the data.

**I have more questions!**

Open a discussion on the [GitHub Discussions page](https://github.com/sep-developers/sep/discussions/categories/q-a)!
