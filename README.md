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


# This does not check if the endpoint is actually available (done lazily when
# the repos is actually accessed), but LocalDirectoryRepository does check
# whether the path/URL is actually local. FlatHTMLRepository automatically
# converts file: URLs to paths, so the fetcher implementation does not need
# to worry about special-casing them.
repos = [
    SimpleRepository('https://pypi.org/simple'),
    FlatHTMLRepository('https://pypackages.mydomain.com/find-links.html'),
    FlatHTMLRepository('/path/to/find-links.html'),
    LocalDirectoryRepository('/path/to/local/directory'),
]

requirement = Requirement('pip>=10,<18')

for repo in repos:
    fetcher = RequestsFetcher(repo, requirement)
    for entry in fetcher:
        print(entry)

    # Maybe it is a good idea to separate base classes for fetchers? 
    # SynchronousFetcher implements ``__next__``, and AsynchronousFetcher
    # implements ``__anext__``.
    fetcher = AIOHTTPFetcher(repo, requirement)
    async for entry in fetcher:
        print(entry)
```
