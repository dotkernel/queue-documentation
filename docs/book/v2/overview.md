# Overview

## Summary

Dotkernel Queue is a Symfony Messenger-based component that lets Mezzio and Laminas
applications hand off slow or unreliable work to background workers instead of
processing it inline.

## Details

> [Dotkernel Queue](https://github.com/dotkernel/queue) is a component based on [**Symfony Messenger**](https://github.com/symfony/messenger) that is used to queue asynchronous tasks.
[netglue/laminas-messenger](https://github.com/netglue/laminas-messenger) is an adapter that integrates Symfony Messenger with the [Laminas Service Manager](https://docs.laminas.dev/laminas-servicemanager/) container for Mezzio/Laminas applications.

Some everyday **operations are time-consuming and resource-intensive**, so it's best if they run on separate machines, decoupled from the regular request-response cycle.
**Asynchronous execution** performed by background workers ensures that these operations won't overload the main platform.
It allows the main platform to return a response and remain responsive for new requests, while tasks with long execution times are scheduled to run later.

![Queue process](https://docs.dotkernel.org/img/queue/schema.png)

## Badges

![OSS Lifecycle](https://img.shields.io/osslifecycle/dotkernel/queue)
![PHP from Packagist (specify version)](https://img.shields.io/packagist/php-v/dotkernel/queue/2.0.0)

[![GitHub issues](https://img.shields.io/github/issues/dotkernel/queue)](https://github.com/dotkernel/queue/issues)
[![GitHub forks](https://img.shields.io/github/forks/dotkernel/queue)](https://github.com/dotkernel/queue/network)
[![GitHub stars](https://img.shields.io/github/stars/dotkernel/queue)](https://github.com/dotkernel/queue/stargazers)
[![GitHub license](https://img.shields.io/github/license/dotkernel/queue)](https://github.com/dotkernel/queue/blob/2.0/LICENSE.md)

[![Build Status](https://github.com/dotkernel/queue/actions/workflows/continuous-integration.yml/badge.svg?branch=2.0)](https://github.com/dotkernel/queue/actions/workflows/continuous-integration.yml)
[![codecov](https://codecov.io/gh/dotkernel/queue/branch/2.0/graph/badge.svg?token=pexSf4wIhc)](https://codecov.io/gh/dotkernel/queue)
[![Qodana](https://github.com/dotkernel/queue/actions/workflows/qodana_code_quality.yml/badge.svg?branch=2.0)](https://github.com/dotkernel/queue/actions/workflows/qodana_code_quality.yml)
[![PHPStan](https://github.com/dotkernel/queue/actions/workflows/static-analysis.yml/badge.svg?branch=2.0)](https://github.com/dotkernel/queue/actions/workflows/static-analysis.yml)

## FAQ

**Q: What is Dotkernel Queue built on?**

A: It's based on Symfony Messenger, integrated into Mezzio/Laminas applications through
the `netglue/laminas-messenger` adapter for the Laminas Service Manager container.

**Q: Why run tasks asynchronously instead of inline?**

A: Time-consuming or resource-intensive operations would otherwise block the
request-response cycle; running them on background workers keeps the main platform
responsive to new requests.

**Q: Where can I find the project's build and license status?**

A: See the badges above, which link to the GitHub issues, forks, stars, license, CI,
code coverage, and static analysis pages for the `dotkernel/queue` repository.
