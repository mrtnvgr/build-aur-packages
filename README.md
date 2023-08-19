# `build-aur-packages`

![test status](https://github.com/mrtnvgr/build-aur-packages/actions/workflows/test.yml/badge.svg)

Github Action that builds AUR packages and provides the built packages as
package repository in the github workspace.
From there, other actions can use the package repository to install packages or upload the repository to some share or ...

See
[here for a real world example](https://github.com/mrtnvgr/binaur).

Usage:

```yaml
jobs:
  build_repository:
    runs-on: ubuntu-latest
    steps:
    - name: Build Packages
      uses: mrtnvgr/build-aur-packages@main
      with:
        packages: |
          azure-cli
          kwallet-git
          micronucleus-git
        missing_pacman_dependencies: |
          libusb-compat
```

This example will build packages

```
          azure-cli
          kwallet-git
          micronucleus-git
```

Since the package `micronucleus-git` has the dependencies not properly
declared, you can force `pacman` to install the missing dependency by passing
it to `missing_pacman_dependencies`.
If a dependency from AUR is missing, you can pass this to
`missing_aur_dependencies`.

The resulting repository information will be copied to the github workspace.

# Development

To build a package and create the corresponding repository files, build the docker image

    docker build -t builder .

then run it, passing the packages as environment variables.
The names of the variables are derived from the `action.yaml`.

    mkdir workspace
    docker run --rm -it \
        -v $(pwd)/workspace:/workspace \
        -e "GITHUB_WORKSPACE=/workspace" -e "INPUT_PACKAGES=go-do" \
        builder
