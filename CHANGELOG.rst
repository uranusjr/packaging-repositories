0.2.0 (2018-09-26)
==================

The Fetcher initializer now accepts a package name (as string) instead of a
requirement object. Specifier filtering is now provided by the filter feature.

A new set of classes are added to support declarative filtering of fetched
file entries. A base class Filter is introduced for subclassing, and two
built-in filters are provided for filtering by version and requires-python.


0.1.0 (2018-09-25)
==================

Initial release. A minimal, sans I/O repository fetcher is implemented. Users
of the implementation should subclass ``Fetcher`` and implement either
``__next__`` or ``__anext__`` to generate entry instances.

The implementation should do the followings:

1. Call ``self.iter_endpoints()`` to get an iterator of endpoints, and loop
   through them. There are three possible endpoints:

   * ``endpoint.local == False``. This points to a remote (HTTP or HTTPS) HTML
     page. The page should be accessed to obtain the HTML as text.
   * ``endpoint.local == True``, and ``endpoint.value`` points to a directory.
     The directory’s listing should be obtained as a collection.
   * ``endpoint.local == True``, and ``endpoint.value`` points to a file. The
     file should be treated as a HTML, and its content obtained as text.

2. Call ``self.iter_entries(endpoint, value)``, and yield each entry from the
   iterator. ``endpoint`` would be the endpoint object used, and ``value`` the
   value obtained from the previous step.
