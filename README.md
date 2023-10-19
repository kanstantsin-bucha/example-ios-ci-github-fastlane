# example-ios-ci-github-fastlane

This repository shows example of the CI-CD setup for an iOS project using Github Actions and Fastlane.

It is fully production ready for an big app. Not a pet project setup.


# Client iOS and Desktop macOS 

## Description

This repository contains setup for the iOS client app and the macOS desktop app.

## Tech Stack

Application should be written in [Swift Programming Language](https://www.swift.org/) version 5.7, using the following iOS fameworks

- [SwiftUI](https://developer.apple.com/xcode/swiftui/) - For writing all user interface code
- [Strutured Concurrency](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency/) - handling asyncronous operations
- [Combine](https://developer.apple.com/documentation/combine) - Reactive approach for data processing
- [Swift Package Manager](https://www.swift.org/package-manager/) - Dependency manager


## Companion components and repositories

[`match-certs`](https://github.com/kanstantsin-bucha/example-ios-ci-github-fastlane-certs.git) - repo with provisioninig profiles and code signing identities used in the app. Check `Code signing` section for more info


## Prerequisites and Environment

The project runs in multiple environments (staging/production) specified by the
* application schemes
* config files
* entitlements

- AppConfig.swift file contains secrets(environment variables) for the app
This file should be added to .gitignore and created locally and on CI using the `CreateAppConfigFromEnv` script.

Before running the Fastlane lane, make sure to install gems by running `bundle install`. 
When dealing with `fastlane` in Workflow, make sure to use `bundle exec fastlane` to ensure proper version.

## How to build the project

The project has two main application targets with multiple associated schemes.
The schemes uses different entitlements and xcconfigs

### Targets & Schemes
- Client
  - ClientStaging
  - ClientProduction
  - ClientAppStore
- Desktop
  - Desktop
- AppThirdPartyPackage
- ClientUnitTests
- ClientUITests

The project uses [swiftPM](https://www.swift.org/package-manager/) to manage dependencies, so the only thing you should do is check out the repo and open the `xcodeproj` file and allow SPM to resolve all package dependencies, select the preferred scheme you want to build and hit CMD+B or use `xcodebuild` specifying the scheme. 

## Code signing

We're using [match](https://docs.fastlane.tools/actions/match/) to fetch all provisoning profiles and code signing identities for us. Run

* `fastlane load_provision_profiles_ios_staging`
* `fastlane load_provision_profiles_ios_production`

To install necessary profiles and certificates required in code signing.

## Branching strategy

This project follows [`git-flow`](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) and Continuos Integration rules. Meaning that

- `main` branch contains stable code reflecting the current production version
- `develop` branch contains code which is the latest and may not be stable
- `feature/[TicketNumber]-human-readable-description-of-feature`
- `fix/[TicketNumber]-human-readable-descriptionof-bug`
- `chore/[TicketNumber]-human-readable-description-of-improvement`

**Never create PRs or merge changes to the `main` branch directly. The only way is to create a PR from the `develop`.**

Create PR for the changes. Pass a code review. Merge the PR to the `develop` branch. The build will be automatically triggered if Unit Tests finihed with success. The build will be shipped to the Staging TestFlight.

## Pull Requests

Each pull request name should contain ticket number. Follow the example:
* Feature-[TicketNumber]: Ticket title copied from PM system like Jira, Shortcut, Asana
* Fix-[TicketNumber]: Ticket title copied from PM system like Jira, Shortcut, Asana
* Chore-[TicketNumber]: Ticket title copied from PM system like Jira, Shortcut, Asana

To merge PR you need at least one approve and Unit Tests finihed with success.

Feel free to add more details on the changes you made in the description of PR. 
Attach screenshots or a shot video in case the UI was implemented. 

## Releasing new versions

New versions should be released by opening a new PR against the `main` branch from `develop`. After the merge, the build will be triggered automatically and shipped to the Production TestFlight.

