# Contributing to the Nexpose Coverage Toolkit

The users and maintainers of Nexpose Coverage Toolkit would greatly appreciate any contributions
you can make to the project.  These contributions typically come in the form of
filed bugs/issues or pull requests (PRs). 

## Contributing Issues / Bug Reports

If you encounter any bugs or problems with the Coverage Toolkit, please file them
[here](https://github.com/rapid7/coverage-toolkit/issues/new), providing as much detail as
possible.  If the bug is straight-forward enough and you understand the fix for
the bug well enough, you may take the simpler, less-paperwork route and simply
fill a PR with the fix and the necessary details.

## Contributing Code

The Coverage Toolkit uses a model nearly identical to that of
[Metasploit](https://github.com/rapid7/metasploit-framework) as outlined
[here](https://github.com/rapid7/metasploit-framework/wiki/Setting-Up-a-Metasploit-Development-Environment),
at least from a ```git``` perspective.  If you've been through that process
(or, even better, you've been through it many times with many people), you can
do exactly what you did for Metasploit but with Coverage Toolkit and ignore the rest of
this document.

On the other hand, if you haven't, read on!

### Fork and Clone

Generally, this should only need to be done once, or if you need to start over.

1. Fork Recog: Visit https://github.com/rapid7/coverage-toolkit and click Fork,
   selecting your github account if prompted
2.  Clone ```git@github.com:<your-github-username>/coverage-toolkit.git```, replacing
```<your-github-username>``` with, you guessed it, your Github username.
3.  Add the master Recog repository as your upstream:
```
git remote add upstream git://github.com/rapid7/coverage-toolkit.git
git fetch --all
```

### Branch and Improve

If you have a contribution to make, first create a branch to contain your
work.  The name is yours to choose, however generally it should roughly
describe what you are doing.  In this example, and from here on out, the
branch will be FOO, but you should obviously change this

```
git fetch --all
git checkout master
git rebase upstream/master
git checkout -b FOO
```

Now, make your changes, committing as necessary, using useful commit messages:

```
vim CONTRIBUTING.md
git add CONTRIBUTING.md
git commit -m "Add a document on how to contribute to the coverage-toolkit." -a
```

Now push your changes to your fork:

```
git push origin FOO
```

Finally, submit the PR.  Navigate to ```https://github.com/<your-github-username>/coverage-toolkit/compare/FOO```, fill in the details and submit.

## Landing PRs

(Note: this portion is a work-in-progress.  Please update it as things change)

Much like with the process of submitting PRs, the Toolkit's process for landing PRs
is very similar to [Metasploit's process for landing
PRs](https://github.com/rapid7/metasploit-framework/wiki/Landing-Pull-Requests).
In short:

1. Follow the "Fork and Clone" steps from above
2. Update your `.git/config` to ensure that the `remote ["upstream"]` section is configure to pull both branches and PRs from upstream.  It should look something like the following, in particular the second `fetch` option:

    ```
     [remote "upstream"]
      url = git@github.com:rapid7/coverage-toolkit.git
      fetch = +refs/heads/*:refs/remotes/upstream/*
      fetch = +refs/pull/*/head:refs/remotes/upstream/pr/*
     ```
3. Fetch the latest revisions, including PRs:

    ```
    git fetch --all
    ```
4. Checkout and branch the PR for testing.  Replace ```PR``` below with the actual PR # in question:

    ```
    git checkout -b landing-PR upstream/pr/PR
    ```
5. Test the PR, which typically involves running ```rspec```.
6. Merge with master, re-test, validate and push:

    ```
    git checkout -b upstream-master --track upstream/master
    git merge -S --no-ff --edit landing-PR # merge the PR into upstream-master
    # re-test if/as necessary
    git push upstream upstream-master:master --dry-run # confirm you are pushing what you expect
    git push upstream upstream-master:master # push upstream-master to upstream:master
    ```
7. If applicable, release a new version (see next section)

## Releasing New Versions

At this time the Coverage Toolkit is a sandbox for review and development.  There are not yet Toolkit releases, but there may be in the future.  Contributors can use the content here as custom content in their Nexpose instances and some of the contributions will make their way (manually, with attribution) into shipping Nexpose coverage.  Over time we intend to evolve this to a more mature process whereby the master branch always contains the latest validated, tested, community driven coverage.
