# PHP Afterlife

PHP updates for Debian and Ubuntu releases past their end of life.

When a release reaches end of life, [deb.sury.org](https://deb.sury.org/) stops building PHP for it. PHP Afterlife picks up from there: the same packages, rebuilt for the retired release, in an APT repository your servers can keep upgrading from.

Website: https://phpafterlife.hyscaler.org/

## Status

- **Ready now:** Debian 11 bullseye packages for amd64.
- **Being built:** arm64 packages for Debian 11 bullseye.
- **Coming soon:** Ubuntu releases past their end of life.
- **For early testers:** these builds have not been tested yet. They are built with `nocheck`, which skips the upstream test suites. The tests will be run; until then, try the packages on a test server before production.

## Is this for you?

Only if your release is past its end of life.

- **Your release is still supported** (Debian 12 and 13, Ubuntu LTS releases in support): stay with [deb.sury.org](https://deb.sury.org/), the repository for Debian and the `ondrej/php` PPA for Ubuntu. It is the original and it gets every update first. Nothing here is built for these releases.
- **Your release is past end of life** (Debian 11 bullseye): Sury has stopped building for it and removed its packages. Use this repository to keep receiving PHP security releases while you plan the move to a supported release.

## Releases

| Release | PHP branches | Architectures | Repository |
| --- | --- | --- | --- |
| Debian 11 bullseye | 8.2, 8.3, 8.4, 8.5, 8.6 | amd64 is ready, arm64 is being built | `https://phpafterlife.hyscaler.org/debian` |

Further Debian releases, and Ubuntu releases, are added as they pass their end of life. A PHP branch is carried for as long as php.net supports it.

## Set up on Debian 11 bullseye

Package names are the same as Sury's (`php8.4-fpm`, `php8.3-redis` and so on) and the versions sort above the last ones Sury published, so an existing installation upgrades in place.

1. Install the repository's signing key.

   ```sh
   sudo curl -fsSL -o /usr/share/keyrings/php-afterlife-archive-keyring.gpg \
     https://phpafterlife.hyscaler.org/debian/php-afterlife-archive-keyring.gpg
   ```

2. Check that its fingerprint matches the one under [Signing keys](#signing-keys).

   ```sh
   gpg --show-keys --with-fingerprint \
     /usr/share/keyrings/php-afterlife-archive-keyring.gpg
   ```

3. Add the repository.

   ```sh
   echo "deb [signed-by=/usr/share/keyrings/php-afterlife-archive-keyring.gpg] \
   https://phpafterlife.hyscaler.org/debian bullseye main" \
     | sudo tee /etc/apt/sources.list.d/php-afterlife.list
   ```

4. Find the old Sury entry and delete the file it is in. Its packages are gone, so `apt update` reports errors for as long as it stays.

   ```sh
   grep -rl packages.sury.org /etc/apt/sources.list /etc/apt/sources.list.d/
   ```

5. Upgrade.

   ```sh
   sudo apt update && sudo apt upgrade
   ```

   **If apt says PHP packages are "kept back":** you have debug symbol packages from Sury installed (names ending in `-dbgsym`). Each one requires the exact old version of its PHP package, and this repository has no matching debug build, so apt holds back every package that depends on it. Remove them and upgrade again. They are only used to analyse crash dumps.

   ```sh
   sudo apt remove 'php*-dbgsym' && sudo apt upgrade
   ```

## Signing keys

Debian releases share one repository and one signing key; the release is chosen by its name in the `deb` line.

| Release | Keyring | Fingerprint |
| --- | --- | --- |
| Debian (all releases) | [.gpg](https://phpafterlife.hyscaler.org/debian/php-afterlife-archive-keyring.gpg), [.asc](https://phpafterlife.hyscaler.org/debian/php-afterlife-archive-keyring.asc) | `3248 8407 A75D 6BBA 09D4 4697 B4FD 9E12 71F1 E7AD` |

## What is in it

- The PHP interpreters: CLI, FPM, the Apache module, CGI, embed, phpdbg, and the bundled extensions.
- The PECL extensions from Sury's repository, such as redis, imagick, xdebug, apcu, memcached, mongodb and swoole.
- The supporting libraries Sury backports.
- No debug symbol packages (`-dbgsym`) yet. They are several times the size of everything else and are planned for a later rebuild.

## How it is built

- Sources come from Sury's packages for the oldest release still supported, verified against the signed release file.
- Each one is rebuilt in a clean container of the retired release, for amd64 and arm64.
- A rebuilt package keeps Sury's version with `+afterlife1` appended. It sorts below Sury's own build for the next release, so a later upgrade moves back to Sury cleanly.

## Limits

This keeps PHP current, not the operating system. A release past end of life no longer gets fixes for OpenSSL, glibc, the kernel or anything else PHP runs on. Treat this repository as time to migrate, not a reason to stay.

- The upstream test suites have not been run yet: the current builds use the `nocheck` build option, which skips them. They will be run, and this page will say so when they have. Until then the repository is for early testers.
- If Sury's packaging comes to need a library the retired release cannot provide, the affected package stops at its last buildable version.
- Updates follow Sury's, so they arrive here after they appear there.

## Credits

The packaging is the work of Ondřej Surý and the Debian PHP maintainers, who continue to support and update the releases still in support. This project rebuilds it for releases they no longer target, and is independent of all of them: see the [disclaimer](#disclaimer). If Sury's repository has served you well, [consider supporting it](https://deb.sury.org/#donate).

## Disclaimer

- **Independent project.** PHP Afterlife is built and published by HyScaler. It is not affiliated with, endorsed by, sponsored by or approved by Ondřej Surý, deb.sury.org, the Debian project, Canonical or Ubuntu, The PHP Group, or the authors of the extensions and libraries it packages.
- **Not their packages.** These are unofficial rebuilds. They are not produced, reviewed or supported by Sury or by Debian. Please do not report problems with them to deb.sury.org, Debian, Ubuntu or PHP.
- **Names and trademarks.** PHP, Debian, Ubuntu and the other names used here belong to their respective owners. They appear only to describe what the packages are and where they come from.
- **No warranty.** The packages are provided as is, without warranty of any kind, and you use them at your own risk. Each package remains under its original licence.
- **Where to report.** Problems with these packages go to https://github.com/hyscaler/PHP-Afterlife/issues.

## Problems and questions

Open an issue at https://github.com/hyscaler/PHP-Afterlife/issues.

Built and signed by HyScaler.
