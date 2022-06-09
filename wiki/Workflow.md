### Basics

To ensure a high and consistent standard of code, the C++ team employs:

- a strict "no warnings" attitude
    - TODO - We currently have [warnings which need fixing!](https://github.com/ethereum/webthree-umbrella/issues/219)
- strict adherence to a specific coding style
- a strict peer-review process utilising GitHub's Pull-Request system (no pushes to origin)
- a comprehensive unit & integration test framework
- continuous integration to ensure clean builds and non-failing tests on all supported platforms
- Agile PM methods - either a SCRUM-style tracker ([Pivotal Tracker](https://www.pivotaltracker.com)) or Kanban-style ([Waffle](https://waffle.io)).
- We are moving towards grouping all our GitHub issues ONLY under these repos:
    - [solidity](https://github.com/ethereum/solidity/issues)
    - [mix](https://github.com/ethereum/mix/issues)
    - [webthree-umbrella](https://github.com/ethereum/webthree-umbrella/issues) - any other C++ issue here please!

All C++-team members should conform to this development methodology.

### Initiation

- Everyone should have a GitHub account
- Usage of Waffle comes from signing in through the GitHub account
- Communication is done mostly through the [C++ Gitter Channel](https://gitter.im/ethereum/cpp-ethereum)

### Coding Style

The C++ coding style is defined [here](https://raw.githubusercontent.com/ethereum/webthree-umbrella/develop/CodingStandards.txt).

### No Warnings, cpp-lint

All patches submitted and merged must compile on all platforms with no warnings.

### Peer Review

Our peer review process presently involves: submitting a GHPR (GitHub Pull Request) against the develop branch, tagging it with "pleasereview", enrolling another team member to review the PR and wait until they switch the tag to "looksgood" or "gotissues". In the case of "gotissues", alter the PR to address the issues and repeat. In the case of "looksgood" and if at least two core team members (this might include the original author) are confident with the change, it can be merged.

The develop branch should always be in a "ready to use" state, it cannot be any less stable than the release branch, although it might include unfinished features (which should be either turned off or hidden from the user).

### Testing

All self-contained units should include a suite of unit tests. Protocol additions should have the test data be in the tests repository. All other components should have a set of custom tests in the tests directory.

When unit tests are difficult, integration tests should be included in the same place.

### Documentation

Please use doxygen-style documentation in the source code and also write appropriate end-user documentation for new features. The documentation should (if available) be placed in the `docs` subfolder of the mix, solidity webthree-umbrella (otherwise) sub-repository.

### CI

No patch should be submitted unless it builds cleanly on all platforms of the CI System (Jenkins) and all tests pass.

### Agile PM

As a team member you and anyone else in your project should, as a whole, elect to use either Pivotal Tracker (a SCRUM-like time & task tracking system that works in parallel to GitHub's issues tracker) or Waffle (a Kanban-like task tracking system that works on top of GitHub's issues tracker).

You must use one or the other.

### In General

Fix things in the code base as you see them. If you see a bug, even if it's not your "territory", treat the whole code as your responsibility and fix. Same goes for coding style. And documentation. And tests. And code reviews.

### Language

In general use British English, and keep thesaurus.com handy for helping with nomenclature.