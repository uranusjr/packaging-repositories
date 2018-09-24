# packaging-repositories: pip package finder extraction prototype.

Discussion: https://github.com/pypa/pip/issues/5800


Proposed API:

```python
from packaging.requirement import Requirement
from packaging_repositories import (
    Fetcher, SimpleRepository, FlatHTMLRepository, LocalDirectoryRepository,
)


class RequestsFetcher(Fetcher):
    """Fetch implementation using requests to perform remote requests.
    """


class AIOHTTPFetcher(Fetcher):
    """Fetch implementation using AIOHTTP to perform remote requests.
    """


# All kinds of repositories should be able to parse URLs and local paths.
# This does not check if the endpoint is actually available (done lazily when
# the repos is actually accessed), but LocalDirectoryRepository does check
# whether the path/URL is actually local.
repos = [
    SimpleRepository('https://pypi.org/simple'),
    FlatHTMLRepository('https://pypackages.mydomain.com/find-links.html'),
    FlatHTMLRepository('/path/to/find-links.html'),
    LocalDirectoryRepository('/path/to/local/directory'),
]

requirement = Requirement('pip>=10,<18')

for repo in repos:
    # Not sure if it is best to inherit the link model.
    # If the second argument is None, *all* links are returned. This would
    # be useful for find-links repos, and possible for the simple API (by
    # inspecting the root page), although not necessarily a good thing.
    fetcher = RequestsFetcher(repo, requirement)
    for link in fetcher:
        print(link)

    # It is up to the fetcher class to decide how to yield links. Maybe it
    # is a good idea to separate base classes for sync and async fetchers?
    # SynchronousFetcher implements ``__iter__``, and AsynchronousFetcher
    # implements ``__aiter__``.
    fetcher = AIOHTTPFetcher(repo, requirement)
    async for link in fetcher:
        print(link)
```
