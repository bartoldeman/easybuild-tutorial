# Contributing to EasyBuild

## Contribution procedure

There are several ways to contribute to EasyBuild, including:

* providing feedback;
* reporting bugs;
* joining the discussions (mailing list, Slack, conf calls);
* sharing suggestions/ideas for enhancements & additional features;
* contributing easyconfigs, enhancing easyblocks, adding support for new software, implementing additional features, ...;
* extending & enhancing documentation;

To contribute changes to the code or easyconfigs, you should:

0) Create and setup a [GitHub account](https://github.com/join) (and register your SSH public key);

1) First clone and fork the appropriate GitHub repository, for example:

```shell
git clone git@github.com:easybuilders/easybuild-easyconfigs.git
cd easybuild-easyconfigs
git remote add my_fork git@github.com:example/easybuild-easyconfigs.git
```

You should change '`example`' in the last line to your own GitHub user name!

2) Create and check out a new branch:

```
git checkout -b example
```

3) Stage the changes you want to contribute:

```shell
git add easybuild/easyconfigs/e/example-1.2.3.eb
```

4) Commit those changes with a sensible commit message:

```shell
git commit -m "This is just an example"
```

5) Push your branch to your fork of the repository on GitHub:

```shell
git push my_fork example
```

6) Open the pull request through the GitHub web interface, making sure that:

* the target branch is correct (should be `develop`);
* an appropriate title is used;
* a short description of the changes is provided;
* the changes are indeed the ones you want to propose;
* clicking the (correct) green button;

In addition, for easyconfig files you should make sure that they have the
[correct filename](../basic_usage/#easyconfig-filenames), and that they are located in the correct subdirectory.

<div align="center"><img src="../../img/pfft.png" alt="Pfft" width="30%"/></div>

That didn't exactly motivate you to contribute, did it...

## Github integration features

Over the years we noticed that some people were keen on contributing to EasyBuild,
but they were not very familiar with Git or GitHub. That meant they had to overcome a
relatively steep learning curve before they could contribute...

<div align="center"><img src="../../img/no_git.png" alt="Gandalf vs Git" width="50%"/></div>

In addition, the contribution workflow can be a bit daunting and time consuming,
even if you're already familiar with the procedure. You will have dozens of
branches flying around in no time, and if you get stuck in a weird corner
with `git` you may quickly end up demotivated.

This is frustrating not only for the people who wanted to contribute but
also for the EasyBuild maintainers, and it doesn't agree with the philosophy of
a project that aims to *automate* tedious software installation procedures.

At the end of 2015 efforts were made to tackle this issue by implementing
GitHub integration features in EasyBuild, which automate the contribution
workflow by running `git` commands and interacting with the [GitHub API](https://developer.github.com/v3/).

We will briefly go over some of these features here, but they are also covered in detail [in the EasyBuild documentation](https://easybuild.readthedocs.io/en/latest/Integration_with_GitHub.html).

### Requirements & configuration

First of all, the GitHub integration features impose a couple of additional [requirements](https://easybuild.readthedocs.io/en/latest/Integration_with_GitHub.html)
and configuration.


**Additional dependencies**

Both the `GitPython` and `keyring` Python packages as well as the `keyrings.cryptfile` add-on package must be installed.
In the prepared environment, you can do this via:

```shell
pip3 install --user GitPython keyring keyrings.cryptfile
```

**SSH public key in GitHub account**

You need to have a GitHub account that has your SSH public key registered in it
(via [https://github.com/settings/keys](https://github.com/settings/keys)).

If you need to generate an SSH key pair, you can run the following command:

```shell
ssh-keygen -t rsa -b 4096
```

You can copy the SSH public key from the output of this command:

```shell
cat .ssh/id_rsa.pub
```

!!! Warning
    If you are using the prepared tutorial environment,
    we strongly recommend you:

    * to **protect the SSH key pair you create with a password**;
    * to **remove the public key from your GitHub account** after completing this
      part of the tutorial;

    Keep in mind that anyone who obtains your SSH *private* key and knows
    the password to unlock it can push changes to your GitHub repositories!


**Forked repository in GitHub**

In addition, you must have *forked* the EasyBuild repository you want to contribute to
(for example [https://github.com/easybuilders/easybuild-easyconfigs](https://github.com/easybuilders/easybuild-easyconfigs)).

**EasyBuild configuration, incl. GitHub token**

You also have to configure EasyBuild a bit more, so it knows about your
GitHub user name *and* has a GitHub token available in order to perform actions
in GitHub with your credentials.

To do this, you should define the `github-user` configuration option and
run the "`eb --install-github-token`" command:

```shell
# replace 'ebtutorial' with your own GitHub username!
$ export EASYBUILD_GITHUB_USER=ebtutorial
$ eb --install-github-token
```

To create a GitHub token:

* Visit [https://github.com/settings/tokens](https://github.com/settings/tokens).
* Click *"Personal access tokens"*.
* Click followed by *"Generate new token"*.
* Give the token a name (for example *"Token for EasyBuild"*).
* Select both the '`repo`' and '`gist`' scopes.
* Click the green *"Generate token"* button.
* Copy the generated token.
* Paste the token when asked by `--install-github-token` (and hit *Enter*).
* Enter a password to encrypt your GitHub token.

The output should look something like this:

```shell
$ eb --install-github-token
== temporary log file in case of crash /tmp/eb-9z0bdve9/easybuild-hfpti62w.log
Token: 
Validating token...
Token seems to be valid, installing it.
Please set a password for your new keyring: 
Please confirm the password:
Token 'fed..987' installed!
```

!!! Warning
    If you are using the prepared tutorial environment,
    we strongly recommend you to **revoke the GitHub token** 
    again from your GitHub account after completing this part of the tutorial.

    Anyone who knows the GitHub token can perform actions in your name on GitHub
    through the GitHub API!


**Checking status of GitHub integration**

You can check the status of the GitHub integration using "`eb --check-github`":

```shell
$ eb --check-github
== temporary log file in case of crash /tmp/eb-4ckdlyfy/easybuild-gp69ev2w.log

Checking status of GitHub integration...

Making sure we're online...OK

* GitHub user...ebtutorial=> OK
Please enter password for encrypted keyring:
* GitHub token...fed..987 (len: 40) => OK (validated)
* git command...OK ("git version 1.8.3.1; ")
* GitPython module...OK (GitPython version 3.1.3)
Enter passphrase for key '/home/easybuild/.ssh/id_rsa': 
Enter passphrase for key '/home/easybuild/.ssh/id_rsa': 
* push access to ebtutorial/easybuild-easyconfigs repo @ GitHub...OK
Enter passphrase for key '/home/easybuild/.ssh/id_rsa':
* creating gists...OK
* location to Git working dirs... not found (suboptimal)

All checks PASSed!

Status of GitHub integration:
* --from-pr: OK
* --new-pr: OK
* --review-pr: OK
* --update-pr: OK
* --upload-test-report: OK
```

If you see '`OK`' for each of the status checks, you're all set
to try out the GitHub integration features!

!!! Note
    If your SSH private key is protected with a password, you may need
    to enter your password a couple of times when running "`eb --check-github`".

    You can avoid this by [using an SSH agent](https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent).

### Creating pull requests

The GitHub integration in EasyBuild allows you to **create pull requests
using the `eb` command**, without even leaving your shell environment.
How cool is that‽

To create a pull request to the `easybuild-easyconfigs` repository,
you can either do it in a single go by
running "`eb --new-pr`" and passing it one or more easyconfig files to add
into the pull request.

The more detailed option is to first create a branch in your repository fork
in GitHub via "`eb --new-branch-github`" and then later open the pull request
via "`eb --new-pr-from-branch`". This method can be useful when preparing multiple
interdependent pull requests, or to check whether your changes pass the unit tests
(which are run automatically in the GitHub Actions CI environment for
all branches pushed to your fork).

The `--new-pr` option can also be used to open pull requests to the easyblocks
and framework repositories, and it will even automatically determine the target
repository based on the contents of the files you provide. Whoa!

You can control the target repository for your pull request using
`--pr-target-account` (default is `easybuilders`) and `--pr-target-repo`.

If you want you can carefully double check your work before actually
opening the pull request by doing a dry run via "`eb --dry-run --new-pr`"
or "`eb -D --new-pr`".

Finally, you can use "`eb --preview-pr`" to see how the easyconfig files
for which you plan to create a pull request differ from existing easyconfig
files.

### Updating pull requests

To update an existing pull request with additional changes
you can use "`eb --update-pr`" and pass the pull request ID,
alongside the paths to the updated files.

If you have only created a branch (for example via `eb --new-branch-github`)
you can update it via `--update-branch-github` in the same way,
passing the branch name instead of a pull request ID.

### Using a pull request

Next to creating and updating branches and pull requests
you can also *use* easyconfig files and easyblocks from a pull request,
regardless of its status (open, merged, or closed). This is particularly
useful when testing contributions, or to install software for which 
support is not yet included in the latest EasyBuild release.

Using the `--from-pr` option you can install easyconfig files from the
pull request with specified ID. By default all easyconfig files that are
touched by the pull request will be installed, but you can specify
particular ones to use as well. It is generally advised to also use the
`--robot` option to ensure that the easyconfig files are installed in the
correct order with respect to dependencies.

Similarly, using a new or updated easyblock from a pull request is as simple
as using the `--include-easyblocks-from-pr` option. And of course you can
combine it with `--from-pr`!

Via `--upload-test-report` you can let EasyBuild submit a comment into the
easyconfig pull request to show that the installation worked on your system. This is
useful for others to know, in particular EasyBuild maintainers, since the comment
will include information about your system (OS, processor, etc.) and your EasyBuild configuration.

## Demo

That is a lot to digest, so let us make this a bit more concrete with an example:
we will open a pull request for the [`eb-tutorial` example software](../adding_support_software/#example) to *a fork* of the [`easybuild-easyconfigs` repository](https://github.com/easybuilders/easybuild-easyconfigs) using the `eb` command,
and submit a test report in it.

!!! Note
    Make sure that you have correctly configured the GitHub integration,
    [see above](#requirements-configuration).

### Creating pull request

We first configure EasyBuild to target the `boegel` GitHub account rather
than the default `easybuilders` GitHub organisation,
by defining the `pr-target-account` configuration setting:

```shell
export EASYBUILD_PR_TARGET_ACCOUNT=boegel
```

In the output of "`eb --show-config`" you should see a line like this:

```
pr-target-account (E) = boegel
```

We only do this to avoid that lots of pull requests for the `eb-tutorial`
example software are opened in the [central easyconfigs repository](https://github.com/easybuilders/easybuild-easyconfigs).

Opening a pull request is as simple as running "`eb --new-pr`" and passing
the easyconfig file:

```shell
$ eb --new-pr eb-tutorial.eb 
== temporary log file in case of crash /tmp/eb-ggr6scbq/easybuild-hnk271xj.log
== found valid index for /home/example/.local/easybuild/easyconfigs, so using it...
== fetching branch 'develop' from https://github.com/boegel/easybuild-easyconfigs.git...
== copying files to /tmp/eb-ggr6scbq/git-working-dirxwk1fzaw/easybuild-easyconfigs...
== pushing branch '20200622095415_new_pr_eb-tutorial100' to remote 'github_ebtutorial_qgtfU' (git@github.com:ebtutorial/easybuild-easyconfigs.git)
Enter passphrase for key '/home/example/.ssh/id_rsa': 
Please enter password for encrypted keyring: 

Opening pull request
* target: boegel/easybuild-easyconfigs:develop
* from: ebtutorial/easybuild-easyconfigs:20200622095415_new_pr_eb-tutorial100
* title: "{tools}[GCC/9.3.0] eb-tutorial v1.0.0"
* labels: new
* description:
"""
(created using `eb --new-pr`)

"""
* overview of changes:
 easybuild/easyconfigs/e/eb-tutorial/eb-tutorial-1.0.0-GCC-9.3.0.eb | 26 ++++++++++++++++++++++++++
 1 file changed, 26 insertions(+)

Opened pull request: https://github.com/boegel/easybuild-easyconfigs/pull/65
== Temporary log file(s) /tmp/eb-ggr6scbq/easybuild-hnk271xj.log* have been removed.
== Temporary directory /tmp/eb-ggr6scbq has been removed.
```

Take a moment to grasp what we did here: we ran **a single `eb` command** which
took care of the **[whole contribution procedure](#contribution-procedure)** for us, including:

* Cloning the `easybuilders/easybuild-easyconfigs` repository and checking out the `develop` branch (in a temporary
  directory);
* Picking a sensible name for a branch and creating it;
* Adding the `eb-tutorial` easyconfig file to the branch, in the correct location
  (`easybuild/easyconfigs/e/eb-tutorial/`) and with the correct filename (`eb-tutorial-1.0.0-GCC-9.3.0.eb`);
* Pushing the branch to our fork (`example/easybuild-easyconfigs`);
* Actually opening the pull request, using an informative title.

That is so... easy!

This feature not only *significantly* lowers the bar for contributing,
it also saves quite a bit of time since you don't need to double check
various details (like targeting the `develop` branch) or spend time on
coming up with a nice looking title or funny branch name (although you
still can if you really want to).

There are a couple of nice side effects too, like not having any local branches
to tidy up on once the pull request gets merged (since `--new-pr` created the
branch only in a temporary directory).

If many contributions are made via `--new-pr` it also simplifies the task
of EasyBuild maintainers, since pull requests opened this way have a particular
structure to them and thus are easier to digest because they look familiar.

!!! Warning
    If you are using the prepared tutorial environment,
    **do not forget to remove the GitHub public key and GitHub token**
    that you created earlier for this part of the tutorial from your GitHub account!

### Uploading test report

After opening the pull request, we should also upload a test report to show that the installation is working.
This is just as easy as creating the pull request.

First make sure that the pre-installed software in the prepared environment
is available, since the required dependencies for `eb-tutorial` are already
installed there:

```shell
module use /easybuild/modules/all
```

You can verify which dependencies are still missing using `--from-pr` combined with `--missing`:

```shell
# change '65' to the ID of your own pull request (see output of --new-pr)
$ eb --from-pr 65 --missing
== temporary log file in case of crash /tmp/eb-ioi9ywm1/easybuild-e3v0xa1b.log
Please enter password for encrypted keyring: 
== found valid index for /home/example/.local/easybuild/easyconfigs, so using it...

1 out of 20 required modules missing:

* eb-tutorial/1.0.0-GCC-9.3.0 (eb-tutorial-1.0.0-GCC-9.3.0.eb)
```

Uploading a test report boils down to combining `--from-pr` with `--upload-test-report`:

```shell
# change '65' to the ID of your own pull request (see output of --new-pr)
$ eb --rebuild --from-pr 65 --upload-test-report
Please enter password for encrypted keyring: 
...
== processing EasyBuild easyconfig /tmp/eb-bnb1pv3n/files_pr65/e/eb-tutorial/eb-tutorial-1.0.0-GCC-9.3.0.eb
== building and installing eb-tutorial/1.0.0-GCC-9.3.0...
...
== COMPLETED: Installation ended successfully (took 2 sec)
...
Adding comment to easybuild-easyconfigs issue #65: 'Test report by @ebtutorial
**SUCCESS**
Build succeeded for 1 out of 1 (1 easyconfigs in this PR)
example - Linux centos linux 7.8.2003, x86_64, Intel(R) Core(TM) i5-7360U CPU @ 2.30GHz (haswell), Python 3.6.8
See https://gist.github.com/f7c74159c809029afd99e30e4d994ef1 for a full test report.'
== Test report uploaded to https://gist.github.com/f7c74159c809029afd99e30e4d994ef1 and mentioned in a comment in easyconfigs PR#65
```

Note that we may need to use `--rebuild` here since `eb-tutorial` may already be installed.

This results in a comment being added to the pull request:

<div align="center"><img src="../../img/test_report_comment.png" alt="Test report comment" width="75%"/></div>

The gist linked from this comment provides more detailed information:

<div align="center"><img src="../../img/test_report_gist.png" alt="Test report gist" width="75%"/></div>

## Contribution stats

To wrap up, let us look at some statistics about the contributions that have
been made to EasyBuild over the years.

### Unique contributors

Since EasyBuild went public in 2012 more than 250 different people have made
contributions to the `easybuild-easyconfigs` repository. We also recently
passed the threshold of 100 unique contributors in the easyblocks repository,
and at the time of writing we were just *one* contributor short of reaching
100 unique contributors to the EasyBuild framework repository.

<div align="center"><img src="../../img/contributors_overview_all_easybuild_repos.png" alt="EasyBuild contributor overview" width="75%"/></div>

If we look at unique contributors per year, we see that the number of contributors
to the easyconfigs repository has been gradually increasing over the years,
while the number of unique contributors to the framework and easyblocks repositories has
largely remained stable. Making contributions to the latter
two repositories requires significantly more effort, which explains the difference
with the easyconfigs repository.
<div align="center"><img src="../../img/unique_contributors_per_year.png" alt="Overview of unique contributors per year across EasyBuild repositories" width="75%"/></div>

### Pull requests per year (easyconfigs)

Plotting the number of pull requests to the easyconfigs repository per year
clearly shows an increase, with almost 2,100 *merged* contributions in 2019.
In total over 2,600 pull requests were merged in 2019 across all EasyBuild repositories.

It is also worth noting that in 2019 about two thirds of all contributions were made by people outside
of the HPC-UGent team.

<div align="center"><img src="../../img/contrib_stats_easyconfigs_pr_counts.png" alt="Overview of PRs to easybuild-easyconfigs repo, per year" width="70%"/></div>

Looking at the ratio of pull requests that were created using `--new-pr` versus
the ones that were created manually shows a steady increase in adoption 
since 2016 the GitHub integration feature. Throughout 2019, 77% of all pull requests
were made using `--new-pr`, and we see this increasing further in 2020.

<div align="center"><img src="../../img/overview_easyconfig_prs_new_pr_vs_manual.png" alt="Overview of PRs to easybuild-easyconfigs, created with --new-pr vs manually" width="70%"/></div>
