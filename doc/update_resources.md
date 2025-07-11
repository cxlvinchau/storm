# Update shipped third party resources


## Eigen

In Eigen, we have adapted `SparseLU` to work with scalar types that do not default construct from a double (like CLN numbers) or that do not have an operator< or std::abs

To update the Eigen version, just change the corresponding commit hash in `$STORM_DIR/resources/3rdparty/CmakeLists.txt`.
Check whether the patch located at `$STORM_DIR/resources/3rdparty/patches/eigen.patch` can be applied without issues (in particular check for changes in `Eigen/src/SparseLU/`)

In case a new patch needs to be created follow these steps:

1. Clone `https://gitlab.com/libeigen/eigen.git` somewhere and checkout the previously shipped version
2. Checkout a new branch e.g., `git branch storm-patch; git checkout storm-patch`
3. Apply the old patch via `git apply $STORM_DIR/resources/3rdparty/patches/eigen.patch`. At this point, `git diff` shows you all the changes we apply to Eigen
4. Make a commit, e.g., `git commit -a -m "Storm patch"`
5. Merge or rebase the new Eigen tag, branch or commit, e.g., `git rebase <new_commit_hash>`
6. Resolve issues, make changes, and commit them
7. Create a new patch file via `git format-patch <new_commit_hash> --stdout > eigen.patch`, where `<new_commit_hash>` is the tag, branch or commit from step 5
8. add the patch to resources/patches/ and change the resources/3rdparty/CmakeLists.txt file accordingly.


## googletest / gtest

To update gtest, simply download the new sources from [here](https://github.com/google/googletest/releases) and put them to `$STORM_DIR/resources/3rdparty/googletest`.

The currently shipped version can be shown using

```console
grep GOOGLETEST_VERSION $STORM_DIR/resources/3rdparty/googletest/CMakeLists.txt
```

We add some extra code to gtest located in `$STORM_DIR/src/test/storm_gtest.h`. Note that our code might not be compatible with future versions of gtest.


## Gurobi

To support newer versions of Gurobi, adapt `$STORM_DIR/resources/cmake/find_modules/FindGUROBI.cmake` with the new version numbers.


## nlohmann/json for Modern C++

The currently shipped version is forked from the [official GitHub](https://github.com/nlohmann/json) commit `6eab7a2b187b10b2494e39c1961750bfd1bda500`.
We extended the library towards rational numbers, see [here](../resources/3rdparty/modernjson/README_STORM.md).
To update, you can follow these steps:

1. Check out the above commit in a separate repository
2. Copy the contents of `$STORM_DIR/resources/3rdparty/modernjson/include` to the repository you just checked out and commit to a fresh branch
3. The diff for that commit shows you the exact modifications we made.
4. Merge the json version you want to update to into your branch.
5. Resolve potential conflicts and review what has changed, in particular if it affects handling of floating point numbers.
6. When this is all done, copy the contents back into the storm directory. Make sure to not apply any unnecessary code formatting to keep the diff smallish.
7. *Update the commit hash mentioned in this document*


## parallel hashmap

Download the new sources from [GitHub](https://github.com/greg7mdp/parallel-hashmap) and put them to `$STORM_DIR/resources/3rdparty/parallel_hashmap/`.
Remove directories that are not needed, e.g, `rm -r doc examples html css benchmark tests index.html`.


## Spot

To update (shipped version of Spot), just change the `SPOT_SHIPPED_VERSION` in `$STORM_DIR/resources/3rdparty/include_spot.cmake`.


## Sylvan & Lace

The currently shipped version of [sylvan](https://github.com/trolando/sylvan) is based on commit b08d75eb56461178a614188ad94cbab211adc253 (tag 1.7.1) but with parts of the build system updated to a newer version.
Our Sylvan version also includes [lace](https://github.com/trolando/lace) which is currently based on commit 3577d983e8c40e276fb8070dc4c12c68940e2f2c (tag 1.4.0)
To update, you can follow these steps:

1. Check out the above commit in a separate repository
2. Copy the contents of `$STORM_DIR/resources/3rdparty/sylvan` to the repository you just checked out and commit to a fresh branch
3. The diff for that commit shows you the modifications we made to the sylvan source code. 
   We also include *lace* (`lace.c` and `lace.h`) which is a dependency of sylvan that is normally downloaded by sylvan's build scripts via cmake `FetchContent`.
   As this requires cmake 3.14 and complicates the overall build process, we decided to ship the files `lace.c` and `lace.h` directly (see Storm commit 095e78a897b01db20df95bca89e746229e6a85c8)
4. Merge the `master` of sylvan (or whatever version you want to update to) into your branch
5. Resolve potential conflicts and review what has changed, in particular if it affects the API that Storm uses
6. Update the shipped `lace` sources.
7. When this is all done, copy the contents back into the storm directory
8. *Update commit hashes of sylvan and lace mentioned in this document* 
