# Rake task for FPM-package as-stats

Create a Debian/Ubuntu package of as-stats using FPM and rake.

"a simple tool to generate per-AS traffic graphs from NetFlow/sFlow records
by Manuel Kasper, Monzoon Networks AG <mkasper@monzoon.net>"

Source: https://neon1.net/as-stats/

# Requirements

* Rake

# Includes

* upstart job
* defaults file
* Net::sFlow Perl module

# Usage

* rake clean: remove all created dirs
* rake prepare[1.36]: downloads and prepares as-stats version 1.36
* rake buld: creates debian package
