git-credential-pass
===================

A simple opinionated, dependency-less [git-credential-helper][] for [pass][].

There is also [pass-git-helper][], which is a more flexible credential helper,
at the cost of requiring additional configuration and the `xdg` Python
dependency.

Installation
------------

1. Include the `git-credential-pass` file in your `PATH` or `GIT_EXEC_PATH`.
2. Configure Git to use it:

       git config -g credential.helper "pass --prefix YOUR_STORE_PREFIX"
       git config -g credential.useHttpPath true

   For other options, run `git-credential-helper -h`.

3. Correctly set up pass and structure your password store accordingly.


Usage
-----

This helper assumes that your password store is structured like this:

    PREFIX/SUB.DOMAIN.TLD/PATH/REPO

The `PREFIX` is freely configurable and and has the default value of `git`.
Multiple host name variations are considered, by successively dropping
subdomains until only the primary domain remains.
A host with a port specification (e.g. `:8080`) will be considered
distinct and a host without port will not be considered in this case.
The same approach is used for simplifying the path, however a better
matching domain always takes precedence.

An example should make the system clear. Let us assume `PREFIX` is `.`,
so the entries are all in the root of the password store.
Given a URL `https://www.github.com/cassava/git-credential-pass.git`,
the following entries will be tried, in order:

    www.github.com/cassava/git-credential-pass
    www.github.com/cassava
    www.github.com
    github.com/cassava/git-credential-pass
    github.com/cassava
    github.com

Note that it is possible to have both an entry and a directory with the
same name, since under-the-hood, the entry has a `.gpg` extension.

    github.com
    github.com/organization

Each pass entry should be formatted like:

    PASSWORD_OR_TOKEN
    user: USER

Optionally, you can name the field `username`. If the user field is omitted,
you may be asked by Git. If a user has already been specified to
Git, it will not be overwritten. All further content is ignored.

Questions and Answers
---------------------

**Can I use age?**

> Yes. Set the options `--pass-exe` and `--suffix` accordingly.

**How can I specify hosts with ports?**

> Use the format `localhost:8080` or `localhost#8080`.
>
> If you use your password-store on Windows, then you should use the version
> with the hash `#` instead of the colon `:`.

**Can I specify a range or a glob?**

> Not yet.

**It's not finding my password!**

> You can debug git-credential-pass by making it verbose with the `-v` flag.
> Alternatively, use the testmode:
>
>     git-credential-pass get -v --test github.com/user/repo


[git-credential-helper]: https://git-scm.com/docs/gitcredentials
[pass-git-helper]: https://github.com/languitar/pass-git-helper
[pass]: https://www.passwordstore.org/
