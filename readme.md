# RAVE for WordPress

RAVE is an automated tool which facilitates being able to verify that various distribution channels of WordPress are serving the legitimate code built from the canonical source code and that it hasn't been tampered with.

Tooling is included to run the verification yourself. A CI system runs on GitHub Actions which verifies a number of official sources and a number of official and unofficial distributions and compares them all against one another to identify any anomolies.

RAVE stands for Reproduce And VErify.

## Why?

## How?

## Reproducible WordPress

## Verifiable WordPress

## What's tested?

Type      | Official | Status | Source
--------- | -------- | ------ | ------
Package   | Yes      | ✅ Done   | `wordpress.org/wordpress-{tag}.zip`
Package   | Yes      | ✅ Done   | `wordpress.org/wordpress-{tag}.tar.gz`
Package   | Yes      | ✅ Done   | `downloads.wordpress.org/release/wordpress-{tag}.zip`
Package   | Yes      | ✅ Done   | `downloads.wordpress.org/release/wordpress-{tag}.tar.gz`
Package   | Yes      | ✅ Done   | `github.com/WordPress/WordPress/archive/refs/tags/{tag}.zip`
Package   | Yes      | ⏳ Todo   | `github.com/WordPress/WordPress/archive/refs/tags/{tag}.tar.gz`
Source    | Yes      | ✅ Done   | `github.com/WordPress/wordpress-develop/tree/{tag}/src`
Source    | Yes      | ✅ Done   | `core.trac.wordpress.org/browser/tags/{tag}/src?format=zip`
Build     | Yes      | ⏳ Todo   | `core.svn.wordpress.org/tags/{tag}/`
Source    | Yes      | ⏳ Todo   | `develop.svn.wordpress.org/tags/{tag}/src/`
Build     | Yes      | ⏳ Todo   | `core.git.wordpress.org` at `{tag}`
Source    | Yes      | ⏳ Todo   | `develop.git.wordpress.org` at `{tag}`
Build     | Yes      | 🤨 TBD    | `build.svn.wordpress.org` ??
Build     | No       | ⏳ Todo   | `packagist.org/packages/roots/wordpress-full` at `{tag}`
Build     | No       | ⏳ Todo   | `github.com/johnpbloch/wordpress-core/tree/{tag}`
Build     | No       | ⏳ Todo   | `packagist.org/packages/johnpbloch/wordpress` at `{tag}`
Package   | Yes      | 🤨 TBD    | `wordpress.org/latest.zip`
Package   | Yes      | 🤨 TBD    | `wordpress.org/latest.tar.gz`

To investigate:

* WP-CLI
* https://api.wordpress.org/core/version-check/1.7/
* APT packages, eg `apt install wordpress`
* wp-env
* What can we do with the md5 and sha1 hashes on .org?
* The container at https://hub.docker.com/_/wordpress

## Elsewhere

* https://reproducible-builds.org/
* https://core.trac.wordpress.org/ticket/39309
