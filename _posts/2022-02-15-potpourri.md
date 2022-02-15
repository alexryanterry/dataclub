---
layout: post
title: "Potpourri"
date: 2022-02-15
ready: true
---

## Git Remotes

- `git remote`: list remotes
- `git remote add <name> <url>`: add a remote
- `git push <remote> <local branch>:<remote branch>`: send objects to remote, and update remote reference
- `git branch --set-upstream-to=<remote>/<remote branch>`: set up correspondence between local and remote branch
- `git fetch`: retrieve objects/references from a remote
- `git pull`: same as `git fetch; git merge`
- `git clone`: download repository from remote

A golden rule in programming is that code does not do what you expect it to do, but what you tell it to do.
Bridging that gap can sometimes be a quite difficult feat.
In this lecture we are going to cover useful techniques for dealing with buggy and resource hungry code: debugging and profiling.

## Debugging

### Printf debugging and Logging

"The most effective debugging tool is still careful thought, coupled with judiciously placed print statements" — Brian Kernighan, _Unix for Beginners_.

A first approach to debug a program is to add print statements around where you have detected the problem, and keep iterating until you have extracted enough information to understand what is responsible for the issue.

A second approach is to use logging in your program, instead of ad hoc print statements. Logging is better than regular print statements for several reasons:

- You can log to files, sockets or even remote servers instead of standard output.
- Logging supports severity levels (such as INFO, DEBUG, WARN, ERROR, &c), that allow you to filter the output accordingly.
- For new issues, there's a fair chance that your logs will contain enough information to detect what is going wrong.

[Here](/static/files/logger.py) is an example code that logs messages:

```bash
$ python logger.py
# Raw output as with just prints
$ python logger.py log
# Log formatted output
$ python logger.py log ERROR
# Print only ERROR levels and above
$ python logger.py color
# Color formatted output
```

One of my favorite tips for making logs more readable is to color code them.
By now you probably have realized that your terminal uses colors to make things more readable. But how does it do it?
Programs like `ls` or `grep` are using [ANSI escape codes](https://en.wikipedia.org/wiki/ANSI_escape_code), which are special sequences of characters to indicate your shell to change the color of the output. For example, executing `echo -e "\e[38;2;255;0;0mThis is red\e[0m"` prints the message `This is red` in red on your terminal, as long as it supports [true color](https://github.com/termstandard/colors#truecolor-support-in-output-devices). If your terminal doesn't support this (e.g. macOS's Terminal.app), you can use the more universally supported escape codes for 16 color choices, for example `echo -e "\e[31;1mThis is red\e[0m"`.

The following script shows how to print many RGB colors into your terminal (again, as long as it supports true color).

```bash
#!/usr/bin/env bash
for R in $(seq 0 20 255); do
    for G in $(seq 0 20 255); do
        for B in $(seq 0 20 255); do
            printf "\e[38;2;${R};${G};${B}m█\e[0m";
        done
    done
done
```

### Static Analysis

For some issues you do not need to run any code.
For example, just by carefully looking at a piece of code you could realize that your loop variable is shadowing an already existing variable or function name; or that a program reads a variable before defining it.
Here is where [static analysis](https://en.wikipedia.org/wiki/Static_program_analysis) tools come into play.
Static analysis programs take source code as input and analyze it using coding rules to reason about its correctness.

In the following Python snippet there are several mistakes.
First, our loop variable `foo` shadows the previous definition of the function `foo`. We also wrote `baz` instead of `bar` in the last line, so the program will crash after completing the `sleep` call (which will take one minute).

```python
import time

def foo():
    return 42

for foo in range(5):
    print(foo)
bar = 1
bar *= 0.2
time.sleep(60)
print(baz)
```

Static analysis tools can identify this kind of issues. When we run [`pyflakes`](https://pypi.org/project/pyflakes) on the code we get the errors related to both bugs. [`mypy`](http://mypy-lang.org/) is another tool that can detect type checking issues. Here, `mypy` will warn us that `bar` is initially an `int` and is then casted to a `float`.
Again, note that all these issues were detected without having to run the code.

```bash
$ pyflakes foobar.py
foobar.py:6: redefinition of unused 'foo' from line 3
foobar.py:11: undefined name 'baz'

$ mypy foobar.py
foobar.py:6: error: Incompatible types in assignment (expression has type "int", variable has type "Callable[[], Any]")
foobar.py:9: error: Incompatible types in assignment (expression has type "float", variable has type "int")
foobar.py:11: error: Name 'baz' is not defined
Found 3 errors in 1 file (checked 1 source file)
```

In the shell tools lecture we covered [`shellcheck`](https://www.shellcheck.net/), which is a similar tool for shell scripts.

Most editors and IDEs support displaying the output of these tools within the editor itself, highlighting the locations of warnings and errors.
This is often called **code linting** and it can also be used to display other types of issues such as stylistic violations or insecure constructs.

In vim, the plugins [`ale`](https://vimawesome.com/plugin/ale) or [`syntastic`](https://vimawesome.com/plugin/syntastic) will let you do that.
For Python, [`pylint`](https://github.com/PyCQA/pylint) and [`pep8`](https://pypi.org/project/pep8/) are examples of stylistic linters and [`bandit`](https://pypi.org/project/bandit/) is a tool designed to find common security issues.
For other languages people have compiled comprehensive lists of useful static analysis tools, such as [Awesome Static Analysis](https://github.com/mre/awesome-static-analysis) (you may want to take a look at the _Writing_ section) and for linters there is [Awesome Linters](https://github.com/caramelomartins/awesome-linters).

A complementary tool to stylistic linting are code formatters such as [`black`](https://github.com/psf/black) for Python, `gofmt` for Go, `rustfmt` for Rust or [`prettier`](https://prettier.io/) for JavaScript, HTML and CSS.
These tools autoformat your code so that it's consistent with common stylistic patterns for the given programming language.
Although you might be unwilling to give stylistic control about your code, standardizing code format will help other people read your code and will make you better at reading other people's (stylistically standardized) code.

For web development, the Chrome/Firefox developer tools are quite handy. They feature a large number of tools, including:

### Web Dev developer tools

For web development, the Chrome/Firefox developer tools are quite handy. They feature a large number of tools, including:

- Source code - Inspect the HTML/CSS/JS source code of any website.
- Live HTML, CSS, JS modification - Change the website content, styles and behavior to test (you can see for yourself that website screenshots are not valid proofs).
- Javascript shell - Execute commands in the JS REPL.
- Network - Analyze the requests timeline.
- Storage - Look into the Cookies and local application storage.

# Dependency management

At a more macro level, your software projects are likely to have
dependencies that are themselves projects. You might depend on installed
programs (like `python`), system packages (like `openssl`), or libraries
within your programming language (like `matplotlib`). These days, most
dependencies will be available through a _repository_ that hosts a
large number of such dependencies in a single place, and provides a
convenient mechanism for installing them. Some examples include the
Ubuntu package repositories for Ubuntu system packages, which you access
through the `apt` tool, RubyGems for Ruby libraries, PyPi for Python
libraries, or the Arch User Repository for Arch Linux user-contributed
packages.

Since the exact mechanisms for interacting with these repositories vary
a lot from repository to repository and from tool to tool, we won't go
too much into the details of any specific one in this lecture. What we
_will_ cover is some of the common terminology they all use. The first
among these is _versioning_. Most projects that other projects depend on
issue a _version number_ with every release. Usually something like
8.1.3 or 64.1.20192004. They are often, but not always, numerical.
Version numbers serve many purposes, and one of the most important of
them is to ensure that software keeps working. Imagine, for example,
that I release a new version of my library where I have renamed a
particular function. If someone tried to build some software that
depends on my library after I release that update, the build might fail
because it calls a function that no longer exists! Versioning attempts
to solve this problem by letting a project say that it depends on a
particular version, or range of versions, of some other project. That
way, even if the underlying library changes, dependent software
continues building by using an older version of my library.

That also isn't ideal though! What if I issue a security update which
does _not_ change the public interface of my library (its "API"), and
which any project that depended on the old version should immediately
start using? This is where the different groups of numbers in a version
come in. The exact meaning of each one varies between projects, but one
relatively common standard is [_semantic
versioning_](https://semver.org/). With semantic versioning, every
version number is of the form: major.minor.patch. The rules are:

- If a new release does not change the API, increase the patch version.
- If you _add_ to your API in a backwards-compatible way, increase the
  minor version.
- If you change the API in a non-backwards-compatible way, increase the
  major version.

This already provides some major advantages. Now, if my project depends
on your project, it _should_ be safe to use the latest release with the
same major version as the one I built against when I developed it, as
long as its minor version is at least what it was back then. In other
words, if I depend on your library at version `1.3.7`, then it _should_
be fine to build it with `1.3.8`, `1.6.1`, or even `1.3.0`. Version
`2.2.4` would probably not be okay, because the major version was
increased. We can see an example of semantic versioning in Python's
version numbers. Many of you are probably aware that Python 2 and Python
3 code do not mix very well, which is why that was a _major_ version
bump. Similarly, code written for Python 3.5 might run fine on Python
3.7, but possibly not on 3.4.

When working with dependency management systems, you may also come
across the notion of _lock files_. A lock file is simply a file that
lists the exact version you are _currently_ depending on of each
dependency. Usually, you need to explicitly run an update program to
upgrade to newer versions of your dependencies. There are many reasons
for this, such as avoiding unnecessary recompiles, having reproducible
builds, or not automatically updating to the latest version (which may
be broken). An extreme version of this kind of dependency locking is
_vendoring_, which is where you copy all the code of your dependencies
into your own project. That gives you total control over any changes to
it, and lets you introduce your own changes to it, but also means you
have to explicitly pull in any updates from the upstream maintainers
over time.

# Continuous integration systems

As you work on larger and larger projects, you'll find that there are
often additional tasks you have to do whenever you make a change to it.
You might have to upload a new version of the documentation, upload a
compiled version somewhere, release the code to pypi, run your test
suite, and all sort of other things. Maybe every time someone sends you
a pull request on GitHub, you want their code to be style checked and
you want some benchmarks to run? When these kinds of needs arise, it's
time to take a look at continuous integration.

Continuous integration, or CI, is an umbrella term for "stuff that runs
whenever your code changes", and there are many companies out there that
provide various types of CI, often for free for open-source projects.
Some of the big ones are Travis CI, Azure Pipelines, and GitHub Actions.
They all work in roughly the same way: you add a file to your repository
that describes what should happen when various things happen to that
repository. By far the most common one is a rule like "when someone
pushes code, run the test suite". When the event triggers, the CI
provider spins up a virtual machines (or more), runs the commands in
your "recipe", and then usually notes down the results somewhere. You
might set it up so that you are notified if the test suite stops
passing, or so that a little badge appears on your repository as long as
the tests pass.

As an example of a CI system, the class website is set up using GitHub
Pages. Pages is a CI action that runs the Jekyll blog software on every
push to `master` and makes the built site available on a particular
GitHub domain. This makes it trivial for us to update the website! We
just make our changes locally, commit them with git, and then push. CI
takes care of the rest.
