# importfiler

Profile your imports, find what is dragging your module load time.

`importfiler` builds on top of the output generated by the interpreter `-X importtime` traces.
It is just a different output targeted more at a specific use-case. Finding what modules impact
the longest on a specific one you are trying to optimize.

If you need a more detailed result, check out `py-spy` or any other runtime profiler.

## Getting `dependencies` import time

Given an import, shows the the time spent importing direct dependencies.
The import time **does include** the time spent on importing other modules.

This helps you finding the most time consuming imports in your library/app.


Examples:

```bash
$ python -m importfiler requests
modules:            time(%)
------------------- --------
requests            100 %
urllib3             61 %
charset_normalizer  11 %
http.cookiejar      10 %
idna                2 %
certifi             2 %
http.cookies        1 %
zipfile             1 %
```


Getting the time in milliseconds:

```bash
$ python -m importfiler --unit=ms requests
modules:            time(ms)
------------------- ---------
requests            48 ms
urllib3             29 ms
charset_normalizer  5 ms
http.cookiejar      4 ms
idna                1 ms
certifi             1 ms
```


## Getting `all_modules` import time

Given an import, shows the the time spent loading all its dependencies (including transitive).
The import time **does not include** the time spent on importing other modules.

This helps you finding the most time consuming modules across your chain of dependencies.


Examples:

```bash
$ python -m importfiler --mode="all_modules" requests
modules:             time (%)             submodules
-------------------- -------------------- --------------------
urllib3              17 %                 urllib3.util.url, urllib3.packages.six, urllib3.exceptions, urllib3.poolmanager, ...
email                7 %                  email._policybase, email.message, email.header, email.feedparser, ...
charset_normalizer   6 %                  charset_normalizer.constant, charset_normalizer.models, charset_normalizer.cd, ...
requests             6 %                  requests.utils, requests.models, requests.exceptions, requests.packages, ...
http                 6 %                  http.cookiejar, http.client, http.cookies
urllib               4 %                  urllib.request, urllib.parse, urllib.error, urllib.response
typing               3 %
ssl                  2 %
importlib            2 %                  importlib.resources._adapters, importlib.resources.abc, importlib.resources._common, ...
re                   2 %                  re._parser, re._compiler, re._constants, re._casefix
```


Getting the time in milliseconds:

```bash
$ python -m importfiler --mode="all_modules" --unit=ms requests
modules:             time (ms)            submodules
-------------------- -------------------- --------------------
urllib3              18 ms                urllib3.util.url, urllib3.packages.six, urllib3.exceptions, urllib3.connectionpool, ...
charset_normalizer   8 ms                 charset_normalizer.md__mypyc, charset_normalizer.constant, charset_normalizer.models, ...
requests             7 ms                 requests.utils, requests.exceptions, requests.models, requests.packages, ...
email                3 ms                 email._policybase, email.header, email.feedparser, email.utils, ...
http                 3 ms                 http.cookiejar, http.cookies, http.client
idna                 2 ms                 idna.core, idna.idnadata, idna.package_data, idna.intranges
urllib               1 ms                 urllib.request, urllib.parse, urllib.error, urllib.response
importlib            1 ms                 importlib.resources._adapters, importlib.resources.abc, importlib.resources._common, ...
typing               1 ms
ssl                  1 ms
re                   1 ms                 re._parser, re._compiler, re._constants, re._casefix
```
