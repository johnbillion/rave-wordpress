# RAVE for WordPress

RAVE for WordPress is an automated tool which compares the contents of published packages of WordPress with the canonical source code to verify they have not been tampered with. The package from WordPress.org, GitHub, and some unofficial packages are all tested.

A CI system runs on GitHub Actions which runs the build process from the canonical sources, fetches published packages from various locations, and compares them all against one another to identify any anomalies. A future version of this library will provide the tooling to run the checks outside of GitHub Actions.

RAVE stands for Reproduce And Verify.

## What's tested?

### Source code

* ✅ `develop.svn.wordpress.org/tags/{tag}/src`
* ✅ `develop.git.wordpress.org` at `{tag}`
* ✅ `github.com/wordpress/wordpress-develop/tree/{tag}/src`
* ✅ `core.trac.wordpress.org/browser/tags/{tag}/src?format=zip`

### Official packages

* ✅ `wordpress.org/wordpress-{tag}.zip` / `.tar.gz`
* ✅ Published md5 and sha1 hashes for the above
* ✅ `downloads.wordpress.org/release/wordpress-{tag}.zip` / `.tar.gz`
* ✅ `build.trac.wordpress.org/browser/tags/{tag}/src?format=zip`
* ✅ `github.com/wordpress/wordpress/archive/refs/tags/{tag}.zip` / `.tar.gz`
* ⏳ Todo: `wordpress.org/latest.zip` / `.tar.gz`

### Official builds

* ⏳ Todo: `core.git.wordpress.org` at `{tag}`
* ⏳ Todo: `core.svn.wordpress.org` at `{tag}`
* ⏳ ???: `build.svn.wordpress.org` at `{tag}`

### Unofficial packages

* ✅ `johnpbloch/wordpress:{tag}` on Packagist
* ✅ `roots/wordpress-full:{tag}` on Packagist
* ⏳ Todo: `roots/wordpress:{tag}` on Packagist

## When do the tests run?

The GitHub Actions workflows run once an hour and currently test:

* WordPress 6.6.2
* WordPress 6.5.5

WordPress 6.7 will be added to the test matrix as soon as it's released.

## Why test the official package?

There are several opportunities for the official WordPress package to be tampered with so that it differs from the actual source code in the source control repos. This could come via an attack on the build server, on wordpress.org or downloads.wordpress.org, from external hackers, from those in control of the wordpress.org CDN, from disgruntled members of the meta or systems teams, from anyone who gains access to accounts or credentials of such team members, or from the project lead.

## Why test unofficial packages?

There are several opportunities for unofficial WordPress packages to be tampered with so they differ from the official package. This could come via an attack on the servers distributing the packages, from external hackers, from those in control of the packaging, or from anyone who gains access to accounts or credentials of people or processes involved in the packaging or distribution.

## How?

By using the `diff` utility to compare the contents of the distributed package at its various locations with the output of building the source code from its various locations, we can identify anomalies between them. This reduces the opportunity for malicious or unwanted code to be introduced into WordPress packages without it also being present in the source repos or pipeline repos.

If one of the GitHub Actions workflows in this repo fails, it should be investigated to see if the failure was caused by divergent code.

## What's not tested?

This approach does not enable detection of:

* Malicious or unwanted code that gets inserted into the Subversion or Git source code repos and makes it into the published package.
* A targeted attack that causes the package to vary based on a factor such as the IP address of the requestor.

## Reproducible WordPress

To quote [reproducible-builds.org](https://reproducible-builds.org/):

> Reproducible builds are a set of software development practices that create an independently-verifiable path from source to binary code.

[The process that builds and packages WordPress](https://build.trac.wordpress.org/timeline) is reproducible, unfortunately the process itself is not open source. The process differs from the `npm run build` process in the source code because it makes some additions (eg. the Akismet plugin) and some exclusions (older default themes).

## Verifiable WordPress

The WordPress.org website [provides the md5 and sha1 hash of its WordPress package files](https://wordpress.org/download/releases/). This is not enough to verify a package on its own, because the hash only represents the zip or tar file and if a package on WordPress.org was altered then its published md5 and sha1 hashes could be altered too.

The WordPress open source project does not make use of package signing which could be used to verify a package. [See ticket #39309 for discussion on this topic](https://core.trac.wordpress.org/ticket/39309).

Therefore, this library has been created to provide a means of verifying that the contents of published packages matches the code in the official source code repos.

Checksums for core files are provided by the WordPress.org API at `https://api.wordpress.org/core/checksums/1.0/?version={tag}`. These are not yet verified by this library but will be soon.

## To investigate

* WP-CLI
* https://api.wordpress.org/core/version-check/1.7/
* APT packages, eg `apt install wordpress`
* wp-env
* Verify published checksums for files: `https://api.wordpress.org/core/checksums/1.0/?version={tag}`
* The container at https://hub.docker.com/_/wordpress

## License

MIT
