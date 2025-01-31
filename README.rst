Python cross-platform network interface discovery
=================================================

.. image:: https://badge.fury.io/py/ifcfg.svg
   :target: https://pypi.python.org/pypi/ifcfg/
.. image:: https://travis-ci.org/ftao/python-ifcfg.svg
  :target: https://travis-ci.org/ftao/python-ifcfg
.. image:: http://codecov.io/github/ftao/python-ifcfg/coverage.svg?branch=master
  :target: http://codecov.io/github/ftao/python-ifcfg?branch=master

Ifcfg is a cross-platform (Windows/Unix) library for parsing ``ifconfig`` and
``ipconfig`` output in Python. It is useful for pulling information such as IP,
Netmask, MAC Address, Hostname, etc.

A fallbacks to ``ip`` is included for newer Unix systems w/o ``ifconfig``. Windows
systems are supported (in English) through ``ipconfig``.

Usage
-----

::

    import ifcfg
    import json

    for name, interface in ifcfg.interfaces().items():
        # do something with interface
        print interface['device']
        print interface['inet']         # First IPv4 found
        print interface['inet4']        # List of ips
        print interface['inet6']
        print interface['netmask']
        print interface['broadcast']

    default = ifcfg.default_interface()

The output of 'ifcfg.interfaces()' dumped to JSON looks something like the
following:

::

    $ python -m ifcfg.cli | python -mjson.tool
    {
        "eth0": {
            "broadcast": "172.16.217.255",
            "ether": "00:0c:29:0c:da:5d",
            "flags": "4163<up,broadcast,running,multicast> ",
            "hostname": "derks-vm.local",
            "inet": "172.16.217.10",
            "inet4": ["172.16.217.10"],
            "inet6": ["fe80::20c:29ff:fe0c:da5d"],
            "mtu": "1500",
            "name": "eth0",
            "netmask": "255.255.255.0",
        },
        "lo": {
            "ether": null,
            "flags": "73<up,loopback,running> ",
            "hostname": "localhost",
            "inet": "127.0.0.1",
            "inet4": ["127.0.0.1"],
            "inet6": ["::1"],
            "mtu": "16436",
            "name": "lo",
            "netmask": "255.0.0.0",
        },
        "virbr0": {
            "broadcast": "192.168.122.255",
            "ether": "52:54:00:5b:70:0d",
            "flags": "4099<up,broadcast,multicast> ",
            "hostname": "derks-vm.local",
            "inet": "192.168.122.1",
            "inet4": ["192.168.122.1"],
            "inet6": [],
            "mtu": "1500",
            "name": "virbr0",
            "netmask": "255.255.255.0",
        }
    }


Development
-----------

To bootstrap development, use a Python virtual environment, and install the dev requirements::

    # Install dev dependencies
    pip install -r requirements_dev.txt
    # Run tests locally
    make test

You can also install tox and run the tests in a specific environment::

    pip install tox
    tox -e py27

Before commiting and opening PRs, ensure that you have pre-commit hooks running::

    pip install pre-commit
    pre-commit install


Release notes
-------------

0.22
____

* Python 3.7 and 3.8 support #51 #53
* Default interface detection on Windows #25 #56
* New flags for unix `ip` command #61

0.21
____

* Force `C` as locale for running commands, to ensure consistent regex patterns #47

0.20
____

* Throw an exception when neither `ip` nor `ifconfig` commands exist #45

0.19
____

* Adds support for interfaces with VLAN notation, e.g. `eth2.2` #40
* Fetch MTU values from `ip` command results #39

0.18
____

* Adds support for interfaces with non-alphanumeric characters, e.g. `eth-int` #35 and #36

0.17
____

* Restore ``ip`` after regressions + add tests
* Add MacOSX support for ``ip`` command

0.16
____

* Support for multiple IPv4 addresses in the new 'inet4' field
* Packaging cleanup

0.15
____

* Support for bridged interface names #24


0.14
____

* Replace Python 2 syntax #21


0.13
____

* Further crashes on non-English Windows systems #17
* Known issue: Localized non-English Windows parsing does not work #18


0.12
____

* Fix encoding crashes on non-English Windows systems


0.11
____

After 6 beta releases, we move on from an idea that this is beta software and instead consider
it to be stable -- we will probably never actually keep up with all the various ways of detecting
network properties for different systems. Anything that is incorrectly detected and can be updated,
can also be implemented and shipped as a new patch release.

So let's **ship early, ship often** instead.

This release seeks to clean up the codebase (sparingly!) and introduce
Windows compatibility.

* Add Windows compatible parsing of ``ipconfig`` output
* Handle non-unicode terminals (Windows+Mac especially)
* Removing ill-defined ``encoding`` keyword arg from ``ifcfg.get_parser``
* Removed no-op Linux Kernel 2.x parsing and ``kernel`` keyword arg
* Removed class ``ifcfg.IfcfgParser``, use ``UnixParser`` instead
* All strings are UTF-8, also in Py 2.7
* Only cross-platform features are now guaranteed to be in the result set:
  ``['inet', 'ether', 'inet6', 'netmask']``
* IPv6 addresses are now stored in a list.
* Removed prefixlen and scopeid, as they should be added for each IPv6 address, not the
  interface
* Allow ``ifcfg`` to be imported despite whether or not the OS system is
  recognized.
* Remove ``ifcfg.exc`` module
* Fix some interface names containing `:_-` characters on Linux (Sergej Vasiljev)


0.10.1
______

* Fixed encoding issues, preventing ``default_interface`` to be detected


0.10
____

* Support for Unix systems w/o ``ifconfig``, for instance newer Ubuntu/Debian
* Refactored to use  ``src/`` hierarchy



License
-------

The Ifcfg library is Open Source and is distributed under the BSD
License (three clause). Please see the LICENSE file included with this
software.
