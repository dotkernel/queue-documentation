# Overview

> [Dotkernel Queue](https://github.com/dotkernel/dot-queue) is a component based on [Symfony Messenger](https://github.com/symfony/messenger) that is used to queue asynchronous tasks.
[netglue/laminas-messenger](https://github.com/netglue/laminas-messenger) is a layer that turns Symfony Messenger into a middleware compatible with Mezzio/Laminas applications.

Some everyday **operations are time-consuming and resource-intensive**, so it's best if they run on separate machines, decoupled from the regular request-response cycle.
**Asynchronous execution** performed by background workers ensures that these operations won't overload the main platform.
It allows the main platform to return a response and remain responsive for new requests, while tasks with long execution times are scheduled to run later.

![Queue process](https://docs.dotkernel.org/img/queue/schema.png)

## Badges

![OSS Lifecycle](https://img.shields.io/osslifecycle/dotkernel/queue)
![PHP from Packagist (specify version)](https://img.shields.io/packagist/php-v/dotkernel/queue/1.0.0)

[![GitHub issues](https://img.shields.io/github/issues/dotkernel/queue)](https://github.com/dotkernel/queue/issues)
[![GitHub forks](https://img.shields.io/github/forks/dotkernel/queue)](https://github.com/dotkernel/queue/network)
[![GitHub stars](https://img.shields.io/github/stars/dotkernel/queue)](https://github.com/dotkernel/queue/stargazers)
[![GitHub license](https://img.shields.io/github/license/dotkernel/queue)](https://github.com/dotkernel/queue/blob/1.0/LICENSE.md)

[![Build Status](https://github.com/mezzio/mezzio-skeleton/actions/workflows/continuous-integration.yml/badge.svg)](https://github.com/mezzio/mezzio-skeleton/actions/workflows/continuous-integration.yml)
[![codecov](https://codecov.io/gh/dotkernel/queue/graph/badge.svg?token=pexSf4wIhc)](https://codecov.io/gh/dotkernel/queue)
[![Qodana](https://github.com/dotkernel/queue/actions/workflows/qodana_code_quality.yml/badge.svg?branch=main)](https://github.com/dotkernel/queue/actions/workflows/qodana_code_quality.yml)
[![PHPStan](https://github.com/dotkernel/queue/actions/workflows/static-analysis.yml/badge.svg?branch=main)](https://github.com/dotkernel/queue/actions/workflows/static-analysis.yml)
