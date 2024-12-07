# pcre2 for MorphOS

Source: [https://github.com/PCRE2Project/pcre2/releases](https://github.com/PCRE2Project/pcre2/releases)

This makefile can be used to compile the above library and create a new release lha archive, to be used with the MorphOS SDK, for native and cross compiling.

## How to compile
```
make init
make build
```

To create a release package the following steps need to be done.
```
make init
make release
```

## Bugs and todo
- the binaries where not linked with z and bz2 libraries