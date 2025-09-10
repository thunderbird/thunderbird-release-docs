## Update sys.path to prefer a comm/third_party/python module

In this case, there was a bug in the third party vendored firefox version of python-hglib 2.6.2.
To work around that bug we vendored python-hglib 2.4 into comm-central.

[In bug 1979430](https://bugzilla.mozilla.org/show_bug.cgi?id=1979430) we reverted comm-central to python-hglib 2.4.
To do this, we:
1. vendored python-hglib 2.4 into comm-central, and
2. ensured sys.path prioritized the comm-central version of python-hglib.

To update sys.path we needed our own version of update-verify-config-creator.py.
Take a look at [the change](https://hg.mozilla.org/releases/comm-beta/rev/a76d409ae824be52b216abee017f93803f77c902) for more details.
