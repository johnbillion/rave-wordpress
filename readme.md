# RAVE for WordPress

RAVE for WordPress is an automated tool which compares the contents of published packages of WordPress with the canonical source code to verify they have not been tampered with. The package from WordPress.org, GitHub, and some unofficial packages are all tested.

A CI system runs on GitHub Actions which runs the build process from the canonical sources, fetches published packages from various locations, and compares them all against one another to identify any anomalies. A future version of this library will provide the tooling to run the checks outside of GitHub Actions.

RAVE stands for Reproduce And Verify.

## What's tested?

### Source code

* ✅ `develop.svn.wordpress.org`
* ✅ `develop.git.wordpress.org`
* ✅ `github.com/wordpress/wordpress-develop`
* ✅ `core.trac.wordpress.org`

### Official packages

* ✅ `wordpress.org/latest.zip`
* ✅ `wordpress.org/wordpress-{tag}.zip`
* ✅ `downloads.wordpress.org/release/wordpress-{tag}.zip`
* ✅ `github.com/wordpress/wordpress`
* ✅ `build.trac.wordpress.org`

The md5 hashes, sha1 hashes, and file checksums published on wordpress.org are also verified for the wordpress.org packages.

### Official builds

* ⏳ Todo: `core.git.wordpress.org`
* ⏳ Todo: `core.svn.wordpress.org`
* ⏳ ???: `build.svn.wordpress.org`

### Unofficial packages

* ✅ `roots/wordpress-full` on Packagist
* ✅ `johnpbloch/wordpress` on Packagist

## When do the tests run?

The GitHub Actions workflow runs once an hour. It tests the latest version in the three most recent branches, which is currently:

* WordPress 6.7
* WordPress 6.6
* WordPress 6.5

## Why test the official package?

There are several opportunities for the official WordPress package to be tampered with so that it differs from the actual source code in the source control repos. This could come via an attack on the build server, on wordpress.org or downloads.wordpress.org, from external hackers, from those in control of the wordpress.org CDN, from disgruntled members of the meta or systems teams, from anyone who gains access to accounts or credentials of such team members, or from the project lead.

## Why test unofficial packages?

There are several opportunities for unofficial WordPress packages to be tampered with so they differ from the official package. This could come via an attack on the servers distributing the packages, from external hackers, from those in control of the packaging, or from anyone who gains access to accounts or credentials of people or processes involved in the packaging or distribution.

## How?

By using the `diff` utility to compare the contents of the distributed package at its various locations with the output of building the source code from its various locations, we can identify anomalies between them. This reduces the opportunity for malicious or unwanted code to be introduced into WordPress packages without it also being present in the source repos or pipeline repos.

If one of the GitHub Actions workflows in this repo fails, it should be investigated to see if the failure was caused by divergent code.

## What's not tested?

This approach does *not*:

* Detect malicious or unwanted code that gets inserted into the Subversion or Git source code repos and subsequently makes it into the published package.
* Detect a targeted attack that can vary the contents of the package based on a parameter such as the IP address making the request.
* Deal with build provenance, attestation, software signing, SBOMs, or make tea.

## Reproducible WordPress

To quote [reproducible-builds.org](https://reproducible-builds.org/):

> Reproducible builds are a set of software development practices that create an independently-verifiable path from source to binary code.

[The process that builds and packages WordPress](https://build.trac.wordpress.org/timeline) is reproducible as far as the built code is concerned, although I believe that the ZIP file generation does not result in a stable hash between invocations. Unfortunately the process itself is not open source. The process differs from the `npm run build` process in the source code because it makes some additions (eg. the Akismet plugin) and some exclusions (older default themes). The verification performed by this repo take this into account.

## Verifiable WordPress

The WordPress.org website [provides the md5 and sha1 hash of its WordPress package files](https://wordpress.org/download/releases/). This is not enough to verify a package on its own, because the hash only represents the zip or tar file and if a package on WordPress.org was altered then its published md5 and sha1 hashes could be altered too.

The WordPress open source project does not make use of package signing which could be used to verify a package. [See ticket #39309 for discussion on this topic](https://core.trac.wordpress.org/ticket/39309).

Therefore, this library has been created to provide a means of verifying that the contents of published packages matches the code in the official source code repos.

Checksums for core files are provided by the WordPress.org API at `https://api.wordpress.org/core/checksums/1.0/?version={tag}`. These are also verified by this library.

## To investigate

* WP-CLI (eg. `wp core download` and `wp core verify-checksums`)
* APT packages, eg `apt install wordpress`
* wp-env
* The container at https://hub.docker.com/_/wordpress

## License

MIT
