# setup-gap V2

This GitHub action downloads and prepares an instance of GAP.
It is intended to be used by the Continuous Integration (CI) action of a GAP
package, that is by an action which runs a package's test suite.

## Supported OSes

This action can be run on macOS and Ubuntu.


## Usage

The action `setup-gap` has to be called by the workflow of a GAP
package.
By default it
- downloads and compiles the master branch of GAP,
- downloads the packages distributed with GAP, and
- compiles the packages `io` and `profiling`

Its behaviour can be customized via the inputs below.

### Inputs

All of the following inputs are optional.

- `GAP_PKGS_TO_CLONE`:
   - A space-separated list of the GAP packages to clone.
   - default: `''`
   - example: `'io autodoc'`
- `GAP_PKGS_TO_BUILD`:
   - A space-separated list of the GAP packages to build. Must include
     `io` and `profiling`.
   - default: `'io profiling'`
- `GAPBRANCH`:
   - The gap branch to clone.
   - default: `master`
- `HPCGAP`:
   - Build HPC-GAP if set to `yes`.
   - default: `no`
- `ABI`:
   - Set to `32` to use 32bit build flags for the package
   - default: `''`

## Contact
Please submit bug reports, suggestions for improvements and patches via
the [issue tracker](https://github.com/gap-actions/setup-gap/issues)
or via email to
[Sergio Siccha](mailto:siccha@mathematik.uni-kl.de).

## License
The action `setup-gap` is free software; you can redistribute
and/or modify it under the terms of the GNU General Public License as published
by the Free Software Foundation; either version 2 of the License, or (at your
opinion) any later version. For details, see the file `LICENSE` distributed
with this action or the FSF's own site.
