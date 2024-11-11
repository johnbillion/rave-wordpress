# RAVE for WordPress

RAVE is an automated tool which facilitates being able to verify that various distribution channels of WordPress are serving the legitimate code built from the canonical source code and that it hasn't been tampered with.

Tooling is included to run the verification yourself. A CI system runs on GitHub Actions which verifies a number of official sources and a number of official and unofficial distributions and compares them all against one another to identify any anomolies.

RAVE stands for Reproduce And VErify.

## Why test the official package?

There are several opportunities for the official WordPress package to be tampered with so that it differs from the actual source code in the source control repos. This could come via an attack on the build server, on wordpress.org or downloads.wordpress.org, from external hackers, from those in control of the wordpress.org CDN, from disgruntled members of the meta or systems teams, from anyone who gains access to accounts or credentials of such team members, or from the project lead.

## Why test unofficial packages?

There are several opportunities for unofficial WordPress packages to be tampered with so they differ from the official package. This could come via an attack on the servers distributing the packages, from external hackers, from those in control of the packaging, or from anyone who gains access to accounts or credentials of people or processes involved in the packaging or distribution.

## How?

By comparing the output of the distributed package at its various locations with the output of building the source code from its various locations, we can identify anomolies between them. This reduces the opportunity for malicious or unwanted code to be introduced into WordPress packages without it also being present in the source repos.

## Reproducible WordPress

The process that builds and packages WordPress is reproducible, unfortunately this process itself is not open source. The process differs from the `npm run build` process in the source code because it makes some additions (eg. the Akismet plugin) and some exclusions (older default themes).

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
