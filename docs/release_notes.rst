.. _changelog:

Release notes
#############

.. figure:: images/pike-release.jpg
    :figwidth: 30%
    :alt: pike fish being release to water
    :align: right

    Releasing a pike.

pikepdf releases use the `semantic versioning <http://semver.org>`_ policy.

The pikepdf API (as provided by ``import pikepdf``) is quite stable and is in production use.

Note that the C++ extension module ``pikepdf._qpdf`` is a private interface within pikepdf that applications should not use directly.

v1.3.0
======

* Remove dependency on ``defusedxml.lxml``. Unfortunately this module of ``defusedxml`` is deprecated because the issue it worked around have been resolved in the underlying ``lxml`` library. In the absence of other options for XML hardening we have reverted to standard ``lxml``.

* Fixed an issue where ``PdfImage.extract_to()`` would write a file in the wrong directory.

* Eliminated an intermediate buffer that was used when saving to an IO stream (as opposed to a filename). We would previously write the entire output to a memory buffer and then write to the output buffer; we now write directly to the stream.

* Added ``Object.emplace()`` as a workaround for when one wants to update a page without generating a new page object so that links/table of contents entries to the original page are preserved.

* Improved documentation. Eliminated all ``arg0`` placeholder variable names.

* Added ``PageList.remove(p=1)``, so that it is possible to remove pages using counting numbers.

v1.2.0
======

* Implemented ``Pdf.close()`` and ``with``-block context manager, to allow Pdf objects to be closed without relying on ``del``.

* ``PdfImage.extract_to()`` has a new keyword argument ``fileprefix=``, which to specify a filepath where an image should be extracted with pikepdf setting the appropriate file suffix. This simplifies the API for the most common case of extracting images to files.

* Fixed an internal test that should have suppressed the extraction of JPEGs with a nonstandard ColorTransform parameter set. Without the proper color transform applied, the extracted JPEGs will typically look very pink. Now, these images should fail to extract as was intended.

* Fixed that ``Pdf.save(object_stream_mode=...)`` was ignored if the default ``fix_metadata_version=True`` was also set.

* Data from one ``Pdf`` is now copied to other ``Pdf`` objects immediately, instead of creating a reference that required source PDFs to remain available. ``Pdf`` objects no longer reference each other.

* libqpdf 8.4.0 is now required

* Various documentation improvements

v1.1.0
======

* Added workaround for macOS/clang build problem of the wrong exception type being thrown in some cases.

* Improved translation of certain system errors to their Python equivalents.

* Fixed issues resulting from platform differences in ``datetime.strftime``. (#25)

* Added ``Pdf.new``, ``Pdf.add_blank_page`` and ``Pdf.make_stream`` convenience methods for creating new PDFs from scratch.

* Added binding for new QPDF JSON feature: ``Object.to_json``.

* We now automatically update the XMP PDFVersion metadata field to be consistent with the PDF's declared version, if the field is present.

* Made our Python-augmented C++ classes easier for Python code inspectors to understand.

* Eliminated use of the ``imghdr`` library.

* Autoformatted Python code with black.

* Fixed handling of XMP metadata that omits the standard ``<x:xmpmeta>`` wrapper.

v1.0.5
======

* Fixed an issue where an invalid date in XMP metadata would cause an exception when updating DocumentInfo. For now, we warn that some DocumentInfo is not convertible. (In the future, we should also check if the XMP date is valid, because it probably is not.)

* Rebuilt the binary wheels with libqpdf 8.3.0. libqpdf 8.2.1 is still supported.

v1.0.4
======

* Updates to tests/resources (provenance of one test file, replaced another test file with a synthetic one)

v1.0.3
======

* Fixed regression on negative indexing of pages.

v1.0.2
======

* Fixed an issue where invalid values such as out of range years (e.g. 0) in DocumentInfo would raise exceptions when using DocumentInfo to populate XMP metadata with ``.load_from_docinfo``.

v1.0.1
======

* Fixed an exception with handling metadata that contains the invalid XML entity ``&#0;`` (an escaped NUL)

v1.0.0
======

* Changed version to 1.0.

v0.10.2
=======

Fixes
-----

* Fixed segfault when overwriting the pikepdf file that is currently open on Linux.

* Fixed removal of an attribute metadata value when values were present on the same node.

v0.10.1
=======

Fixes
-----

* Avoid canonical XML since it is apparently too strict for XMP.

v0.10.0
=======

Fixes
-----

* Fixed several issues related to generating XMP metadata that passed veraPDF validation.

* Fixed a random test suite failure for very large negative integers.

* The lxml library is now required.

v0.9.2
======

Fixes
-----

* Added all of the commonly used XML namespaces to XMP metadata handling, so we are less likely to name something 'ns1', etc.

* Skip a test that fails on Windows.

* Fixed build errors in documentation.

v0.9.1
======

Fixes
-----

* Fix ``Object.write()`` accepting positional arguments it wouldn't use

* Fix handling of XMP data with timezones (or missing timezone information) in a few cases

* Fix generation of XMP with invalid XML characters if the invalid characters were inside a non-scalar object

v0.9.0
======

Updates
-------

* New API to access and edit PDF metadata and make consistent edits to the new and old style of PDF metadata.

* 32-bit binary wheels are now available for Windows

* PDFs can now be saved in QPDF's "qdf" mode

* The Python package defusedxml is now required

* The Python package python-xmp-toolkit and its dependency libexempi are suggested for testing, but not required

Fixes
-----

* Fixed handling of filenames that contain multibyte characters on non-UTF-8 systems

Breaking
--------

* The ``Pdf.metadata`` property was removed, and replaced with the new metadata API

* ``Pdf.attach()`` has been removed, because the interface as implemented had no way to deal with existing attachments.

v0.3.7
======

* Add API for inline images to unparse themselves

v0.3.6
======

* Performance of reading files from memory improved to avoid unnecessary copies.

* It is finally possible to use ``for key in pdfobj`` to iterate contents of PDF Dictionary, Stream and Array objects. Generally these objects behave more like Python containers should now.

* Package API declared beta.

v0.3.5
======

Breaking
--------

* ``Pdf.save(...stream_data_mode=...)`` has been dropped in favor of the newer ``compress_streams=`` and ``stream_decode_level`` parameters.

Fixes
-----

* A use-after-free memory error that caused occasional segfaults and "QPDFFakeName" errors when opening from stream objects has been resolved.

v0.3.4
======

Updates
-------

* pybind11 vendoring has ended now that v2.2.4 has been released

v0.3.3
======

Breaking
--------

* libqpdf 8.2.1 is now required

Updates
-------

* Improved support for working with JPEG2000 images in PDFs
* Added progress callback for saving files, ``Pdf.save(..., progress=)``
* Updated pybind11 subtree

Fixes
-----

* ``del obj.AttributeName`` was not implemented. The attribute interface is now consistent
* Deleting named attributes now defers to the attribute dictionary for Stream objects, as get/set do
* Fixed handling of JPEG2000 images where metadata must be retrieved from the file

v0.3.2
======

Updates
-------

* Added support for direct image extraction of CMYK and grayscale JPEGs, where previously only RGB (internally YUV) was supported
* ``Array()`` now creates an empty array properly
* The syntax ``Name.Foo in Dictionary()``, e.g. ``Name.XObject in page.Resources``, now works

v0.3.1
======

Breaking
--------

* ``pikepdf.open`` now validates its keyword arguments properly, potentially breaking code that passed invalid arguments
* libqpdf 8.1.0 is now required - libqpdf 8.1.0 API is now used for creating Unicode strings
* If a non-existent file is opened with ``pikepdf.open``, a ``FileNotFoundError`` is raised instead of a generic error
* We are now *temporarily* vendoring a copy of pybind11 since its master branch contains unreleased and important fixes for Python 3.7.

Updates
-------

* The syntax ``Name.Thing`` (e.g. ``Name.DecodeParms``) is now supported as equivalent to ``Name('/Thing')`` and is the recommended way to refer names within a PDF
* New API ``Pdf.remove_unneeded_resources()`` which removes objects from each page's resource dictionary that are not used in the page. This can be used to create smaller files.

Fixes
-----

* Fixed an error parsing inline images that have masks
* Fixed several instances of catching C++ exceptions by value instead of by reference

v0.3.0
======

Breaking
--------

* Modified ``Object.write`` method signature to require ``filter`` and ``decode_parms`` as keyword arguments
* Implement automatic type conversion from the PDF Null type to ``None``
* Removed ``Object.unparse_resolved`` in favor of ``Object.unparse(resolved=True)``
* libqpdf 8.0.2 is now required at minimum

Updates
-------

* Improved IPython/Jupyter interface to directly export temporary PDFs
* Updated to qpdf 8.1.0 in wheels
* Added Python 3.7 support for Windows
* Added a number of missing options from QPDF to ``Pdf.open`` and ``Pdf.save``
* Added ability to delete a slice of pages
* Began using Jupyter notebooks for documentation

v0.2.2
======

* Added Python 3.7 support to build and test (not yet available for Windows, due to lack of availability on Appveyor)
* Removed setter API from ``PdfImage`` because it never worked anyway
* Improved handling of ``PdfImage`` with trivial palettes

v0.2.1
======

* ``Object.check_owner`` renamed to ``Object.is_owned_by``
* ``Object.objgen`` and ``Object.get_object_id`` are now public functions
* Major internal reorganization with ``pikepdf.models`` becoming the submodule that holds support code to ease access to PDF objects as opposed to wrapping QPDF.

v0.2.0
======

* Implemented automatic type conversion for ``int``, ``bool`` and ``Decimal``, eliminating the ``pikepdf.{Integer,Boolean,Real}`` types. Removed a lot of associated numerical code.

Everything before v0.2.0 can be considered too old to document.
