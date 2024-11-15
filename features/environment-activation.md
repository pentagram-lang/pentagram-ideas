# Pentagram ideas / Features / Enviroment activation

## Rationale

* Well-used languages need coexisting versions for different workspaces
* IDEs and shells need to be able to easily activate the specific language version
* Each workspace also has its own set of dependencies and CLI tools

## Solution

### Initialization

* Developers install a specific version of Pentagram, e.g. 1.1
* Environments are initialized using `pentagram1.1 init`
* Creates a .pentagram folder together with .envrc and pkg.penta
* .pentagram has a bin subdirectory (which has a `pentagram` symlink) and also stores all of the workspace packages
* pkg.penta has a Pentagram version line
* If running interactive in a direnv-compatible shell ($SHELL) with direnv not installed (`which direnv`), suggest installing direnv
* Prompt to run `direnv allow .` if direnv is installed

### Activation
* For direnv, .envrc has one line `eval "$(pentagram1.1 env bash)"`
* Developers can manually invoke `pentagram1.1 env` for their shell
* IDEs can find the Pentagram version from pkg.penta, and just start up the language server

### Upgrading Pentagram
* Check that new version is installed
* Upgrade pkg.penta
* Upgrade .envrc (prompt with diff if not the exact simple version generated by Pentagram)
