 -- Work in Progress --

### General information

 - Develop always contains the code that will at some point go to the next Release. 

### Versioning Scheme

In our versioning scheme each version is identified by 3 numbers separated by a dot and appearing as `MAJOR.MINOR.PATCH`. e.g. `1.1.3`.

Additional suffixes or labels may follow those 3 numbers. They should be separated by a `-`, eg. `1.1.3-rc1`. Note that for Ubuntu packages that separator has to differ and will be a tilda `~` and as such the corresponding ubuntu example package version would be `1.1.3~rc1`.

### General Release Process Overview
1. A release coordinator is chosen. A developer can nominate him/herself.
2. Release coordinator publishes first RC deadline and RC testing period
3. On deadline, first RC is built and published.
4. We have a period of RC testing. Binaries can be used by users, we update some of our nodes, etc.
5. If bugs are found, they should be fixed, goto 4. If no bugs, current RC is marked as a final version.

### Actions Taken for a Release

 - Merge Develop branch to release.
 - Increment develop's CMakeLists's version to the next version *Note*: Could be done automatically by Jenkins.
 - Reset the `TWEAK` version number in Release's branch CMakeListx.txt to 0.
 - Start a Jenkins Release build, specifying the version as a parameter. The Jenkins version parameter is going to be in the X.Y.Z format. *Note*: Such a Jenkins job is not yet ready.
 - If the build succeeds Jenkins should tag the version given as a parameter appending a build job id. The format should look like `X.Y.Z-buildN`, push the tag to umbrella and upload the binaries to https://build.ethdev.com/cpp-binaries-data/ under a new directory with the given version name.
 - After a succesfull build the `TWEAK` version number in CMakeLists.txt should be incremented. *Note*: Ideally automatically by Jenkins.
 - We can then tag this version also as a Release candidate by pushing a tag in the form of `X.Y.Z-rc1`.
 - Testing phase begins. Reiteration of the above steps may happen in order to get to an RC that is accepted as a final release.