ChangeLog
=========

All notable changes to the project are documented in this file.


[3.0][] - 2017-10-XX
--------------------

This release includes major changes to both the build system and the
`watchdogd` command line interface, making it incompatible with previous
versions.  Therefore the major version number has been bumped.

Application writes can now ask `pkg-config` for `CFLAGS` and `LIBS` to
use the process supervisor interface in `libwdog.so`

Reset cause is now queried and saved in `/var/lib/watchdogd.state` at
boot.  Use the new `watchdogctl` tool to interact with and query status
from the daemon.

### Changes
- Added `watchdogctl` tool to interact with daemon
- New official Watch Dog Detective logo, courtesy of Ron Leishman
- Completely overhauled `watchdogd` command line options and arguments.
  Some options in previous releases were not options but optional
  arguments, while others were useless options for a daemon:
  - Watchdog device node is now an argument not a `-d` option
  - No more `--logfile=FILE` option, redirect `stderr` instead
  - `-n` now prevents the daemon from forking to the background
  - `-f` is now used by the `--filenr` monitor plugin
  - When running in the foreground, output syslog also to `stderr`,
    unless the `-s`, or `--syslog`, option is given
  - `-l, --loglevel` replaces `--verbose` option
  - Use BusyBox options `-T` and `-t` for WDT timeout and kick, this
    replaces the previous `-w` and `-k` options
- No more support for attaching an external supervisor process using
  `SIGUSR1` and `SIGUSR2`
- Conversion to GNU Configure and Build system
- Added `pkg-config` support to `libwdog`
- Save reset cause in `/var/lib/watchdogd.state` by default
- Possible to disble default reset cause backend and plug in your own.
  See `src/rc.h` for the API required of your own backend
- Updates to `libwdog` API, including a compatiblity mode for current
  customer(s) using `watchdogd` 2.0 with a supervisor patch
- Added `libwdog` example clients
- Added customer specific compat `/var/run/supervisor.status`
- Support for delayed reboot in user API, `wdog_reboot_timeout()`


[2.0.1][] - 2016-06-12
----------------------

Minor bugfix release

### Changes
- Update README with simple API example
- Make it possible to run automatic tests as non-root
- Add automatic testing of PMON API to Travis
- Add Coverity Scan

### Fixes
- Silence GNU ar output, has suddenly started warning about `ar crus`.
- Only `write()` to watchdog if descriptor is valid, fixes annoying
  issue with watchdog not being properly disabled with `wdt_enable()`
- Fix issue in `wdt_enable()` which could possible deref. NULL pointer
- Only change enabled state in `wdt_enable()` if operation is succesful


[2.0][] - 2015-09-20
--------------------

This release brings two new plugins and a client side API for process monitoring.

### Changes
- New file descriptor leak monitor plugin, enable with
  `-n, --filenr=VAL`
- New memory leak monitor plugin, enable with
  `m, --meminfo=VAL`
- The loadavg plugin no longer starts automatically, must give
  `-a, --load-average=VAL` command line argument
- Process monitoring plugin with client side instrumentation API
- When connecting to pmon from a monitored client watchdogd now
  raises its priority to `RT_PRIO` 98.  This to ensure proper monitoring
  of processes, which is considered more important than checking if
  there is any CPU slices left.
- When a monitored process misses its deadline the cause is saved
  in `/var/lib/misc/watchdogd.state` so the cause for a reboot can be
  determined at next boot.
- The reboot/reset cause is also saved on SIGPWR, too high loadavg,
  too high memory usage, and file descriptor exhaustion. More on this
  in future releases.
- BusyBox watchdog command line compatibility added.

### Fixes
- Minor cleanup of loadavg plugin
- If the kernel WDT does not reboot the device after 3x timeout watchdogd
  now reboots by itself.


[1.5][] - 2012-11-06
--------------------

This is the last release in the v1.x series. It adds support for:

- `-d <device` to select a different watchdog device node
- `-a <WARN,REBOOT>` to enable loadavg monitoring.  Warn and reboot
  levels are normalized loadavg, no need to take number of CPU cores
  into account.  Feature added by @clockley

The release also inclucdes some clean up and refactoring of the code
base in preparation for the upcoming v2.0 release, which will add
support for process monitoring with an instrumentation API.


[UNRELEASED]: https://github.com/troglobit/watchdogd/compare/3.0...HEAD
[3.0]:        https://github.com/troglobit/watchdogd/compare/2.0.1...3.0
[2.0.1]:      https://github.com/troglobit/watchdogd/compare/2.0...2.0.1
[2.0]:        https://github.com/troglobit/watchdogd/compare/1.6...2.0
[1.6]:        https://github.com/troglobit/watchdogd/compare/1.5...1.6

<!--
  -- Local Variables:
  -- mode: markdown
  -- End:
  -->
