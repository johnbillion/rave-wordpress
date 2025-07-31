[![Reproduce and verify packages](https://github.com/johnbillion/rave-wordpress/actions/workflows/rave.yml/badge.svg?event=schedule)](https://github.com/johnbillion/rave-wordpress/actions/workflows/rave.yml)

# RAVE for WordPress

RAVE for WordPress is a supply chain security tool which compares the contents of published packages of WordPress with the canonical source code to verify they have not been tampered with. The packages from WordPress.org are tested along with those from GitHub and various third parties.

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
* ✅ `api.aspirecloud.net` from AspireCloud
* ✅ Bundle packages provided by FAIR
* ✅ `roots/wordpress-full` on Packagist
* ✅ `johnpbloch/wordpress` on Packagist

### Other verifications

* ✅ md5 and sha1 hashes published on wordpress.org are verified against the wordpress.org packages
* ✅ Checksums returned by `api.wordpress.org/core/checksums` are verified against file contents
* ✅ Update URLs returned by `api.wordpress.org/core/version-check` are verified against expected URLs
* ✅ Versions returned by `api.wordpress.org/core/stable-check` are as expected
* ✅ Verifies that no distributors are offering a version newer than the latest official version

## When do the tests run?

The GitHub Actions workflow runs once an hour. It verifies the latest WordPress release available when it runs.

## Why test the official package?

There are several opportunities for the official WordPress package to be tampered with so that it differs from the actual source code in the source control repos. This could come via an attack on the build server, on wordpress.org or downloads.wordpress.org, from external hackers, from those in control of the wordpress.org CDN, from disgruntled members of the meta or systems teams, from anyone who gains access to accounts or credentials of such team members, or from the project lead.

### Real world examples

* [In 2007 a cracker gained access to the wordpress.org servers and added a backdoor to the WordPress 2.1.1 package](https://wordpress.org/news/2007/03/upgrade-212/). RAVE would have successfully detected this by:
  * Detecting that the 2.1.1 package served by wordpress.org didn't match the package that it reproduced from the source code
  * Making the backdoor code immediately visible in the diff in the failing workflow
* [In 2016 Wordfence identified a vulnerability in a webhook mechanism on api.wordpress.org](https://www.wordfence.com/blog/2016/11/hacking-27-web-via-wordpress-auto-update/) and demonstrated that a cracker could theoretically execute a shell command on the api.wordpress.org server. If a cracker exploited this vulnerability to modify an existing release or create a new one then RAVE would have successfully detected this by:
  * Identifying a new version that didn't exist in the source repos
  * Identifying a modified package that didn't match the code built from the source repos and didn't match the versions published to Packagist and Docker Hub
  * Identifying any attempt to serve an update from an unexpected host name or URL in the update API response
  * Identifying any discrepancies between the published sha1 and md5 hashes, the published checksums, and the offers returned by the update API

## Why test unofficial packages?

There are several opportunities for unofficial WordPress packages to be tampered with so they differ from the official package. This could come via an attack on the servers distributing the packages, from external hackers, from those in control of the packaging, or from anyone who gains access to accounts or credentials of people or processes involved in the packaging or distribution.

## How?

By comparing the contents of the distributed package at its various locations with the output of building the source code, anomalies can be identified. This reduces the opportunity for malicious or unwanted code to be introduced into a WordPress package without it also being present in the source code repos, pipeline repos, and other distribution packages.

If one of the GitHub Actions workflows in this repo fails, it should be investigated to see if the failure was caused by divergent code. Any differences between packages are immediately visible in the workflow run output.

## What's not tested?

This approach:

* Does **not** detect malicious or unwanted code that gets committed to the Subversion source code repo and subsequently makes it into the published package.
* Does **not** detect a targeted attack that causes the contents of the package to vary based on a parameter such as the IP address or user agent of the client making the request.
* Does **not** deal with build provenance verification or software signing.

## Reproducible WordPress

To quote [reproducible-builds.org](https://reproducible-builds.org/):

> Reproducible builds are a set of software development practices that create an independently-verifiable path from source to binary code.

[The process that builds and packages WordPress](https://build.trac.wordpress.org/timeline) is reproducible as far as the built code is concerned, although I believe that the ZIP file generation does not result in a stable hash between invocations. Unfortunately the process itself is not open source. The process differs from the `npm run build` process in the source code because it makes some additions (eg. the Akismet plugin) and some exclusions (older default themes). The verifications performed by this repo take this into account.

## Verifiable WordPress

The WordPress.org website [provides the md5 and sha1 hash of its WordPress package files](https://wordpress.org/download/releases/). This is not enough to verify a package on its own, because the hash only represents the zip or tar file and if a package on WordPress.org was altered then its published md5 and sha1 hashes could be altered too.

The WordPress open source project does not make use of package signing which could be used to verify a package. [See ticket #39309 for discussion on this topic](https://core.trac.wordpress.org/ticket/39309).

Therefore, this library has been created to provide a means of verifying that the contents of published packages matches the code built from the official source code repos.

## License

MIT
