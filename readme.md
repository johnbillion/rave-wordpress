[![Reproduce and verify packages](https://github.com/johnbillion/rave-wordpress/actions/workflows/rave.yml/badge.svg?event=schedule)](https://github.com/johnbillion/rave-wordpress/actions/workflows/rave.yml)

# RAVE for WordPress

RAVE for WordPress is a supply chain security tool which compares the contents of published packages of WordPress with the canonical source code to verify they have not been tampered with. The packages from WordPress.org are tested along with those from GitHub and some unofficial channels.

A CI system runs on GitHub Actions which reproduces the build from the canonical sources, fetches published packages from various locations, and compares them all against one another to verify their integrity and identify any anomalies.

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
* ✅ `downloads.w.org/release/wordpress-{tag}.zip`
* ✅ `github.com/wordpress/wordpress`
* ✅ `build.trac.wordpress.org`

### Official builds

* ✅ `core.git.wordpress.org`
* ✅ `core.svn.wordpress.org`

### Unofficial packages

* ✅ `wordpress` image from Docker Hub
* ✅ `core-updates.wpengine.com` from WPEngine
* ✅ `roots/wordpress-full` on Packagist
* ✅ `johnpbloch/wordpress` on Packagist

### Other verifications

* ✅ md5 and sha1 hashes published on wordpress.org are verified against the wordpress.org packages
* ✅ Checksums returned by `api.wordpress.org/core/checksums` are verified against file contents
* ✅ Update offer URLs returned by `api.wordpress.org/core/version-check` are verified against expected URLs

## When do the tests run?

The GitHub Actions workflow runs once an hour. It verifies the latest version in the three most recent branches, which is currently:

* WordPress 6.7
* WordPress 6.6
* WordPress 6.5

_Note:_ Due to request rate limiting on wordpress.org, the `core.trac.wordpress.org` source and the `build.trac.wordpress.org` package are only verified for the most recent branch. All other sources and packages are verified for the three most recent branches.

## Why test the official package?

There are several opportunities for the official WordPress package to be tampered with so that it differs from the actual source code in the source control repos. This could come via an attack on the build server, on wordpress.org or downloads.wordpress.org, from external hackers, from those in control of the wordpress.org CDN, from disgruntled members of the meta or systems teams, from anyone who gains access to accounts or credentials of such team members, or from the project lead.

### Real world examples

* [In 2007 a cracker gained access to the wordpress.org servers and added a backdoor to the WordPress 2.1.1 package](https://wordpress.org/news/2007/03/upgrade-212/). RAVE would have successfully detected that the 2.1.1 package served by wordpress.org didn't match the package that it reproduced from the source code, and the backdoor code would have been immediately visible in the failing workflow on the RAVE repo.
* [In 2016 Wordfence identified a vulnerability in a webhook mechanism on api.wordpress.org](https://www.wordfence.com/blog/2016/11/hacking-27-web-via-wordpress-auto-update/) and demonstrated that a cracker could theoretically execute a shell command on the api.wordpress.org server. If a cracker exploited this vulnerability to modify an existing release or create a new one then RAVE would have successfully detected it.

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

[The process that builds and packages WordPress](https://build.trac.wordpress.org/timeline) is reproducible as far as the built code is concerned, although I believe that the ZIP file generation does not result in a stable hash between invocations. Unfortunately the process itself is not open source. The process differs from the `npm run build` process in the source code because it makes some additions (eg. the Akismet plugin) and some exclusions (older default themes). The verifications performed by this repo take this into account.

## Verifiable WordPress

The WordPress.org website [provides the md5 and sha1 hash of its WordPress package files](https://wordpress.org/download/releases/). This is not enough to verify a package on its own, because the hash only represents the zip or tar file and if a package on WordPress.org was altered then its published md5 and sha1 hashes could be altered too.

The WordPress open source project does not make use of package signing which could be used to verify a package. [See ticket #39309 for discussion on this topic](https://core.trac.wordpress.org/ticket/39309).

Therefore, this library has been created to provide a means of verifying that the contents of published packages matches the code in the official source code repos.

## License

MIT
