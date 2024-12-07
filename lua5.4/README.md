# lua 5.4 for MorphOS

Source: [https://www.lua.org/download.html](https://www.lua.org/download.html)

This makefile can be used to compile the above library and create a new release lha archive, to be used with the MorphOS SDK, for native and cross compiling.


## How to compile
```
make init
make build
```

To create a release package the following steps need to be taken.
```
make init
make release
```

## Known bugs and todo