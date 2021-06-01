# Swift 5.4 Cross-Module Incremental Compilation Bug

This repository is a reproduction case of a bug in `-enable-experimental-cross-module-incremental-build`.

To reproduce the bug, run `./repo-bug.sh`. In the third run the `B` module doesn't pick up changes in `C_changing.swift`:

```console
* Compiling B *
Job skipped: {compile: B.o <= B.swift}
Job skipped: {merge-module: B.swiftmodule <= B.o}
```

When instead it should see the changes, and produce a compilation error:

```console
* Compiling B *
Queuing because of incremental external dependencies: {compile: B.o <= B.swift}
	interface of top-level name 'FooBar' in /var/folders/mn/njt88rdd1_gckxy_hgq5_9vm0000gp/T/tmp.tkwpo95e/C.swiftmodule -> implementation of source file B.swiftdeps
Blocked by: {compile: B.o <= B.swift}, now blocking jobs: [{merge-module: B.swiftmodule <= B.o}]
Queuing : {merge-module: B.swiftmodule <= B.o}
Adding standard job to task queue: {compile: B.o <= B.swift}
Added to TaskQueue: {compile: B.o <= B.swift}
/var/folders/mn/njt88rdd1_gckxy_hgq5_9vm0000gp/T/tmp.tkwpo95e/B.swift:4:7: error: 'FooBar' initializer is inaccessible due to 'internal' protection level
  _ = FooBar()
      ^
C.FooBar (internal):3:14: note: 'init()' declared here
    internal init()
```
