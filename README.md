# packaging-repositories: pip package finder extraction prototype.


Proposed API:

```python
from packaging_repositories import (
    Fetcher, FlatHTMLRepository,
    LocalDirectoryRepository, SimpleRepository,
)


class RequestsFetcher(Fetcher):
    ...


class AIOHTTPFetcher(Fetcher):
    ...


# FlatHTMLRepository and LocalDirectoryRepository should be able to parse
# URLs. LocalDirectoryRepository would raise ValueError if the URL is not
# ``file:``. This does not check if the endpoint is actually available
# (done lazily when the repos is actually accessed).
repos = [
    SimpleRepository('https://pypi.org/simple'),
    FlatHTMLRepository('/path/to/find-links.html'),
    LocalDirectoryRepository('/path/to/local/directory'),
]

for repo in repos:
    # Not sure if it is best to inherit the link model.
    # If the second argument is None, *all* links are returned. This would
    # be useful for find-links repos, and possible for the simple API (by
    # inspecting the root page), although not necessarily a good thing.
    fetcher = RequestsFetcher(repo, 'pip')
    for link in fetcher:
        print(link)

    # It is up to the fetcher class to decide how to yield links. Maybe it
    # is a good idea to separate base classes for sync and async fetchers?
    # SynchronousFetcher implements ``__iter__``, and AsynchronousFetcher
    # implements ``__aiter__``.
    fetcher = AIOHTTPFetcher(repo, 'pip')
    async for link in fetcher:
        print(link)
```
