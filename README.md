<div align="center">
  <h1>CORE</h1>
  <p>
    <strong>Package core is a service container that elegantly bootstrap and coordinate twelve-factor apps in Go.</strong>
  </p>
  <p>

[![Build](https://github.com/DoNewsCode/core/actions/workflows/go.yml/badge.svg)](https://github.com/DoNewsCode/core/actions/workflows/go.yml)
[![Go Reference](https://pkg.go.dev/badge/github.com/DoNewsCode/core.svg)](https://pkg.go.dev/github.com/DoNewsCode/core)
[![codecov](https://codecov.io/gh/DoNewsCode/core/branch/master/graph/badge.svg)](https://codecov.io/gh/DoNewsCode/core)
[![Go Report Card](https://goreportcard.com/badge/DoNewsCode/core)](https://goreportcard.com/report/DoNewsCode/core)
[![Sourcegraph](https://sourcegraph.com/github.com/DoNewsCode/core/-/badge.svg)](https://sourcegraph.com/github.com/DoNewsCode/core?badge)
 
 </p>
</div>

The twelve-factor methodology has proven its worth over the years. Since its
invention many fields in technology have changed, many among them are shining
and exciting. In the age of Kubernetes, service mesh and serverless
architectures, the twelve-factor methodology has not faded away, but rather has
happened to be a good fit for nearly all of those powerful platforms.

Scaffolding a twelve-factor go app may not be a difficult task for experienced
engineers, but certainly presents some challenges to juniors. For those who are
capable of setting things up, there are still many decisions left to make, and choices
to be agreed upon within the team.

Package core was created to bootstrap and coordinate such services.

## Design Principles

- No package global state.
- Promote dependency injection.
- Testable code.
- Minimalist interface design. Easy to decorate and replace.
- Work with the Go ecosystem rather than reinventing the wheel.
- End to end Context passing.

## Non-Goals

- Tries to be a Spring, Laravel, or Ruby on Rails.
- Tries to care about service details.
- Tries to reimplement the functionality provided by modern platforms.

## Suggested service framework
- [Gin](https://github.com/DoNewsCode/core-gin) (if HTTP only)
- [Go Kit](https://github.com/DoNewsCode/core-kit) (if multiple transports)
- Kratos

## Projects

* [Demo Project](https://github.com/DoNewsCode/skeleton)
* [Starter Template](https://github.com/DoNewsCode/core-starter)
