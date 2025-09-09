# setup-gap

This GitHub action downloads and prepares an instance of GAP.
It is intended to be used by the Continuous Integration (CI) action of a GAP
package, that is by an action which runs a package's test suite.

## Supported OSes

This action can be run on macOS, Ubuntu and Windows (when preceded by the `setup-cygwin` action).


## Usage

The action `setup-gap` has to be called by the workflow of a GAP
package.
By default it downloads and compiles the latest release of GAP.

Its behaviour can be customized via the inputs below.

### Inputs

All of the following inputs are optional.

- `gap-version`:
   - The gap version or branch to build. You may specify "latest" for the latest release, or "devel" for the development version.
     See "Changes to inputs" under "What's new in v3" for more details.
   - default: `latest`
- `repository`
   - The GitHub repository from which to clone GAP.
   - default: `'gap-system/gap'`
- `configflags`:
   - Arguments to pass to the GAP configure script.
   - default: `''`

### What's new in v3
Version v3 contains many changes compared to version v2. When replacing `setup-gap@v2` by `setup-gap@v3` in an existing workflow,
you will have to change the inputs accordingly. We also recommend replacing branches by releases, e.g. `stable-4.14` by `v4.14`.

#### Changes to inputs:
 - The `GAPBRANCH` input has been replaced by `gap-version`, which accepts the following input types:
   - `latest`: this will use the latest release of GAP. This will **not** point to the latest pre-release if it is more recent
     than the latest release.
   - version numbers: e.g. `v4.14.0`, `v4.15.0-beta1`, etc. The leading `v` is optional, the oldest available release is `v4.10.0`.
   - incomplete version numbers: e.g. `v4`, `v4.10`, etc. These will be expanded to the most recent release starting with the incomplete
     version number, e.g. `v4.10` is equivalent to `v4.10.2`. This will **not** expand to pre-releases, and again the leading `v` is
     optional. Use with caution: if you do not quote an input of the form `X.Y`, it will be interpreted as a float. This can cause
     errors, because e.g. `4.10` will become the string `4.1`.
   - branch and tag names: e.g. `master`, `stable-v4.14`, etc. This will use the GAP version built from the corresponding branch or tag.
     NB: the inputs `master`, `main` and `devel` will always point at the "default branch" of the repository, i.e. the branch you are
     presented with when navigating to `github.com/<owner>/<repo>`.
 - The inputs `GAP_PKGS_TO_CLONE` and `GAP_PKGS_TO_BUILD` have been removed. This should now be done by the user in a separate step in
   the workflow, e.g. by using `git clone` or the [PackageManager](https://gap-packages.github.io/PackageManager/) package. See the
   Examples section below.
 - The inputs `HPCGAP` and `ABI` have been removed, and support for both HPC-GAP and 32-bit builds has been removed.
 - The (previously undocumented) input `CONFIGFLAGS` has been renamed to `configflags`.
 - The input `GAP_BOOTSTRAP` has been removed. GAP will always come with all distributed packages.
 - An input `repository` has been added, which allows building a version of GAP hosted on a repository different from `gap-system/gap`.

#### Changes to functionality:
 - There is no longer a separate branch for running this action on Windows. This action should work on Windows, assuming it is
   preceded by version v2 or later of [setup-cygwin](https://github.com/gap-actions/setup-gap).
 - The installation location of GAP is stored in the environment variable `GAPROOT`, which can be used in subsequent steps in the workflow.
 - The GAP executable is added to `PATH`, thus GAP can now always be started by calling `gap`.

### Examples

The following is a minimal example to run this action.

```yaml
name: CI

on:
  push:
  pull_request:

jobs:
  # The CI test job
  test:
    name: CI test
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v5
      - uses: gap-actions/setup-gap@v3
      # ... additional steps using GAP will usually follow here
```

A more extensive example:

```yaml
name: CI

on:
  push:
  pull_request:

jobs:
  # The CI test job
  test:
    name: CI test
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
        gap-version:
          - latest
          - v4.15.0-beta1
          - master

    steps:
      - uses: gap-actions/setup-cygwin@v2
        if: ${{ matrix.os == 'windows-latest' }}
      - uses: actions/checkout@v5
      - uses: gap-actions/setup-gap@v3
        with:
          gap-version: ${{ matrix.gap-version }}
      - shell: bash
        run: |
          # Install package via 'git clone'
          cd $HOME/.gap/pkg
          git clone https://github.com/gap-packages/io
          cd io
          sh autogen.sh
          ./configure --with-gaproot=$GAPROOT
          make -j4
      - shell: bash
        run: |
          # Install packages via PackageManager
          gap -c 'LoadPackage("PackageManager"); InstallPackage("https://github.com/gap-packages/orb.git"); QUIT;'
          gap -c 'LoadPackage("PackageManager"); InstallPackage("cvec"); QUIT;'
```

## Contact
Please submit bug reports, suggestions for improvements and patches via
the [issue tracker](https://github.com/gap-actions/setup-gap/issues).

## License
The action `setup-gap` is free software; you can redistribute
and/or modify it under the terms of the GNU General Public License as published
by the Free Software Foundation; either version 2 of the License, or (at your
opinion) any later version. For details, see the file `LICENSE` distributed
with this action or the FSF's own site.
