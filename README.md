# setup-gap V3

This GitHub action downloads and prepares an instance of GAP.
It is intended to be used by the Continuous Integration (CI) action of a GAP
package, that is by an action which runs a package's test suite.

## Supported OSes

This action can be run on macOS, Ubuntu and Windows (when preceded by the `setup-cygwin` action).


## Usage

The action `setup-gap` has to be called by the workflow of a GAP
package.
By default it
- downloads and compiles the latest release of GAP, and
- compiles the packages `io`, `json` and `profiling`

Its behaviour can be customized via the inputs below.

### Inputs

All of the following inputs are optional.

- `gap-version`:
   - The gap version or branch to build. You may specify "latest" for the latest release, or "default" for the default branch.
   - default: `latest`
- `repository`
   - The GitHub repository from which to clone GAP.
   - default: `'gap-system/gap'`
- `configflags`:
   - Arguments to pass to the GAP configure script.
   - default: `''`
- `gpk-pkgs-to-build`:
   - A space-separated list of the GAP packages to build.
   - default: `'io json profiling'`
- `token`:
   - Token to authenticate with the GitHub API. You should set this to `${{ secrets.GITHUB_TOKEN }}` in your workflow.

## Contact
Please submit bug reports, suggestions for improvements and patches via
the [issue tracker](https://github.com/gap-actions/setup-gap/issues).

## License
The action `setup-gap` is free software; you can redistribute
and/or modify it under the terms of the GNU General Public License as published
by the Free Software Foundation; either version 2 of the License, or (at your
opinion) any later version. For details, see the file `LICENSE` distributed
with this action or the FSF's own site.
