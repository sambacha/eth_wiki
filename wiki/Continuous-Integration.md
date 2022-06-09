We are using Jenkins as our Continuous Integration (CI) system located [here](http://52.28.164.97/).


## Pull Requests

Opening a Pull Request towards any of our repositories will initiate a PR build in Jenkins. 


### Pull Requests with no cross-repo dependencies

If a PR only needs the develop branch of all other projects in order to build then all that needs to be done is to open a PR against a single repository.

### Pull Requests with cross-repo dependencies

If on the other hand the changes need at least 1 other project to be in a branch other than develop then Jenkins needs to know about that. The way this can be done is by specifying the dependencies in the description of a PR.


```
DEPENDS:
{"libweb3core":"crossrepo_testing_branch",
"webthree":"branchthatdoesnotexistandshoulddefaulttodevelop",
"webthreehelpers":"branchname",
"libethereum":"anothername",
"web3js":"yabn",
"solidity":"abranchforsolidity",
"alethzero":"alethzerobranch",
"tests":"testsbranch"
}
```

As can be seen above this is done by giving at the very end of the PR desciption a `DEPENDS:` followed by a newline and then a valid json dictionary containing the repositories that should not be in develop and the branch names they should be in. Anything before the `DEPENDS:` is ignored by Jenkins.

Nothing should follow that dictionary. It should be located at the end of the PR's description.

Please note that the repo names should be absent of `-` and `.` and as such the json dictionary has to contain `webthreehelpers` instead of `webthree-helpers` and `web3js` instead of `web3.js`.

Also note that it has to be valid json so all names must be properly quoted with double/single quotes.

The dictionary does not need to contain all repositories. Any repository not contained, is assumed to request the develop branch. If an invalid repository name is given it's ignored.

Jenkins will try to find the given branches in the fork of the repository that the user who made the PR has. So if the user who made the PR is LefterisJP and the repository is libethereum then Jenkins will try to find the PR in https://github.com/LefterisJP/libethereum. If the branch is not found there, then Jenkins will try to find it in the ethereum upstream. In libethereum's case here: https://github.com/ethereum/libethereum. If the branch is not found in either of them, then and only then the project's branch will default to develop. Jenkins should contain sufficient output at the beginning of each build to determine what branches it uses in building. 

### Don't build a Pull Request

If you need a Pull Request to not be build just put the string `DONTBUILD` anywhere in the PR description and Jenkins will skip the PR.