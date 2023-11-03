Configuring a development environment for Bun can take 10-30 minutes depending on your internet connection and computer speed. You will need ~10GB of free disk space for the repository and build artifacts.

If you are using Windows, you must use a WSL environment as Bun does not yet compile on Windows natively.

Before starting, you will need to already have a release build of Bun installed, as we use our bundler to transpile and minify our code, as well as for code generation scripts.

{% codetabs %}

```bash#Native
$ curl -fsSL https://bun.sh/install | bash # for macOS, Linux, and WSL
```

```bash#npm
$ npm install -g bun # the last `npm` command you'll ever need
```

```bash#Homebrew
$ brew tap oven-sh/bun # for macOS and Linux
$ brew install bun
```

```bash#Docker
$ docker pull oven/bun
$ docker run --rm --init --ulimit memlock=-1:-1 oven/bun
```

```bash#proto
$ proto install bun
```

{% /codetabs %}
## Git Instructions
### Step 1: Fork

Fork the project [on GitHub](https://github.com/oven-sh/bun) and clone your fork
locally.

```bash
git clone git@github.com:username/node.git
cd node
git remote add upstream https://github.com/nodejs/node.git
git fetch upstream
```

### Step 2: Branch

As a best practice to keep your development environment as organized as
possible, create local branches to work within. These should also be created
directly off of the upstream default branch.

```bash
git checkout -b my-branch -t upstream/HEAD
```

### Step 3: Commit

It is a best practice to keep your changes as logically grouped
as possible within individual commits. There is no limit to the number of
commits any single pull request may have, and many contributors find it easier
to review changes that are split across multiple commits.

```bash
git add my/changed/files
git commit
```
#### Commit message guidelines

A good commit message should describe what changed and why.

1. The first line should:

   * contain a short description of the change (preferably 50 characters or
     less, and no more than 72 characters)
   * be entirely in lowercase with the exception of proper nouns, acronyms, and
     the words that refer to code, like function/variable names
   * be prefixed with the name of the changed [subsystem](#appendix-subsystems)
     and start with an imperative verb. Check the output of `git log --oneline
     files/you/changed` to find out what subsystems your changes touch.

   Examples:

   * `net: add localAddress and localPort to Socket`
   * `src: fix typos in async_wrap.h`

2. Keep the second line blank.

3. Wrap all other lines at 72 columns (except for long URLs).

4. If your patch fixes an open issue, you can add a reference to it at the end
   of the log. Use the `Fixes:` prefix and the full issue URL. For other
   references use `Refs:`.

   Examples:

   * `Fixes: https://github.com/oven-sh/bun/issues/1337`
   * `Refs: https://eslint.org/docs/rules/space-in-parens.html`
   * `Refs: https://github.com/oven-sh/bun/pull/3615`

5. If your commit introduces a breaking change (`semver-major`), it should
   contain an explanation about the reason of the breaking change, which
   situation would trigger the breaking change, and what is the exact change.

Sample complete commit message:

```text
subsystem: explain the commit in one line

The body of the commit message should be one or more paragraphs, explaining
things in more detail. Please word-wrap to keep columns to 72 characters or
less.

Fixes: https://github.com/oven-sh/bun/issues/1337
Refs: https://eslint.org/docs/rules/space-in-parens.html
```

If you are new to contributing to Bun, please try to do your best at
conforming to these guidelines, but do not worry if you get something wrong.
One of the existing contributors will help get things situated and the
contributor landing the pull request will ensure that everything follows
the project guidelines.

### Step 4: Rebase

As a best practice, once you have committed your changes, it is a good idea
to use `git rebase` (not `git merge`) to synchronize your work with the main
repository.

```bash
git fetch upstream HEAD
git rebase FETCH_HEAD
```

This ensures that your working branch has the latest changes from `oven-sh/bun`.

### Step 5: Push

Once you are sure your commits are ready to go, with passing tests and linting,
begin the process of opening a pull request by pushing your working branch to
your fork on GitHub.

```bash
git push origin my-branch
```

### Step 6: Opening the pull request

From within GitHub, opening a new pull request will present you with a
[pull request template][]. Please try to do your best at filling out the
details, but feel free to skip parts if you're not sure what to put.

Once opened, pull requests are usually reviewed within a few days.

To get feedback on your proposed change even though it is not ready
to land, use the `Convert to draft` option in the GitHub UI.
Do not use the `wip` label as it might not prevent the PR
from landing before you are ready.

### Step 7: Discuss and update

You will probably get feedback or requests for changes to your pull request.
This is a big part of the submission process so don't be discouraged! Some
contributors may sign off on the pull request right away, others may have
more detailed comments or feedback. This is a necessary part of the process
in order to evaluate whether the changes are correct and necessary.

To make changes to an existing pull request, make the changes to your local
branch, add a new commit with those changes, and push those to your fork.
GitHub will automatically update the pull request.

```bash
git add my/changed/files
git commit
git push origin my-branch
```

If a git conflict arises, it is necessary to synchronize your branch with other
changes that have landed upstream by using `git rebase`:

```bash
git fetch upstream HEAD
git rebase FETCH_HEAD
git push --force-with-lease origin my-branch
```

## Install LLVM

Bun requires LLVM 16 and Clang 16 (`clang` is part of LLVM). This version requirement is to match WebKit (precompiled), as mismatching versions will cause memory allocation failures at runtime. In most cases, you can install LLVM through your system package manager:

{% codetabs %}

```bash#macOS (Homebrew)
$ brew install llvm@16
```

```bash#Ubuntu/Debian
$ # LLVM has an automatic installation script that is compatible with all versions of Ubuntu
$ wget https://apt.llvm.org/llvm.sh -O - | sudo bash -s -- 16 all
```

```bash#Arch
$ sudo pacman -S llvm clang lld
```

```bash#Fedora
$ sudo dnf install 'dnf-command(copr)'
$ sudo dnf copr enable -y @fedora-llvm-team/llvm-snapshots
$ sudo dnf install llvm clang lld
```

{% /codetabs %}

If none of the above solutions apply, you will have to install it [manually](https://github.com/llvm/llvm-project/releases/tag/llvmorg-16.0.6).

Make sure LLVM 16 is in your path:

```bash
$ which clang-16
```

If not, run this to manually link it:

{% codetabs %}

```bash#macOS (Homebrew)
# use fish_add_path if you're using fish
$ export PATH="$PATH:$(brew --prefix llvm@16)/bin"
```

```bash#Arch
# use fish_add_path if you're using fish
$ export PATH="$PATH:/usr/lib/llvm16/bin"
```

{% /codetabs %}

## Install Dependencies

Using your system's package manager, install the rest of Bun's dependencies:

{% codetabs %}

```bash#macOS (Homebrew)
$ brew install automake ccache cmake coreutils gnu-sed go libiconv libtool ninja pkg-config rust
```

```bash#Ubuntu/Debian
$ sudo apt install cargo ccache cmake git golang libtool ninja-build pkg-config rustc ruby-full
```

```bash#Arch
$ sudo pacman -S base-devel ccache cmake git go libiconv libtool make ninja pkg-config python rust sed unzip ruby
```

```bash#Fedora
$ sudo dnf install cargo ccache cmake git golang libtool ninja-build pkg-config rustc libatomic-static libstdc++-static sed unzip
```

{% /codetabs %}

## Install Zig

Zig can be installed either with our npm package [`@oven/zig`](https://www.npmjs.com/package/@oven/zig), or by using [zigup](https://github.com/marler8997/zigup).

```bash
$ bun install -g @oven/zig
$ zigup 0.12.0-dev.1297+a9e66ed73
```

{% callout %}
We last updated Zig on **October 26th, 2023**
{% /callout %}

## Building Bun

After cloning the repository, run the following command to run the first build. This may take a while as it will clone submodules and build dependencies.

```bash
$ bun setup
```

The binary will be located at `./build/bun-debug`. It is recommended to add this to your `$PATH`. To verify the build worked, let's print the version number on the development build of Bun.

```bash
$ build/bun-debug --version
x.y.z_debug
```

To rebuild, you can invoke `bun run build`

```bash
$ bun run build
```

These two scripts, `setup` and `build`, are aliases to do roughly the following:

```bash
$ ./scripts/setup.sh
$ cmake -S . -G Ninja -B build -DCMAKE_BUILD_TYPE=Debug
$ ninja -C build # 'bun run build' runs just this
```

Advanced uses can pass CMake flags to customize the build.

## VSCode

VSCode is the recommended IDE for working on Bun, as it has been configured. Once opening, you can run `Extensions: Show Recommended Extensions` to install the recommended extensions for Zig and C++. ZLS is automatically configured.

## Code generation scripts

{% callout %}

**Note**: This section is outdated. The code generators are run automatically by ninja, instead of by `make`.

{% /callout %}

Bun leverages a lot of code generation scripts.

The [./src/bun.js/bindings/headers.h](https://github.com/oven-sh/bun/blob/main/src/bun.js/bindings/headers.h) file has bindings to & from Zig <> C++ code. This file is generated by running the following:

```bash
$ make headers
```

This ensures that the types for Zig and the types for C++ match up correctly, by using comptime reflection over functions exported/imported.

TypeScript files that end with `*.classes.ts` are another code generation script. They generate C++ boilerplate for classes implemented in Zig. The generated code lives in:

- [src/bun.js/bindings/ZigGeneratedClasses.cpp](https://github.com/oven-sh/bun/tree/main/src/bun.js/bindings/ZigGeneratedClasses.cpp)
- [src/bun.js/bindings/ZigGeneratedClasses.h](https://github.com/oven-sh/bun/tree/main/src/bun.js/bindings/ZigGeneratedClasses.h)
- [src/bun.js/bindings/generated_classes.zig](https://github.com/oven-sh/bun/tree/main/src/bun.js/bindings/generated_classes.zig)
  To generate the code, run:

```bash
$ make codegen
```

Lastly, we also have a [code generation script](src/bun.js/scripts/generate-jssink.js) for our native stream implementations.
To run that, run:

```bash
$ make generate-sink
```

You probably won't need to run that one much.

## Modifying ESM modules

Certain modules like `node:fs`, `node:stream`, `bun:sqlite`, and `ws` are implemented in JavaScript. These live in `src/js/{node,bun,thirdparty}` files and are pre-bundled using Bun. In debug builds, Bun automatically loads these from the filesystem, wherever it was compiled, so no need to re-run `make dev`.

## Release build

To build a release build of Bun, run:

```bash
$ bun run build:release
```

The binary will be located at `./build-release/bun` and `./build-release/bun-profile`.

## Valgrind

On Linux, valgrind can help find memory issues.

Keep in mind:

- JavaScriptCore doesn't support valgrind. It will report spurious errors.
- Valgrind is slow
- Mimalloc will sometimes cause spurious errors when debug build is enabled

You'll need a very recent version of Valgrind due to DWARF 5 debug symbols. You may need to manually compile Valgrind instead of using it from your Linux package manager.

`--fair-sched=try` is necessary if running multithreaded code in Bun (such as the bundler). Otherwise it will hang.

```bash
$ valgrind --fair-sched=try --track-origins=yes bun-debug <args>
```

## Building WebKit locally + Debug mode of JSC

{% callout %}

**TODO**: This is out of date. TLDR is pass `-DUSE_DEBUG_JSC=1` or `-DWEBKIT_DIR=...` to CMake. it will probably need more fiddling. ask @paperdave if you need this.

{% /callout %}

WebKit is not cloned by default (to save time and disk space). To clone and build WebKit locally, run:

```bash
# once you run this, `make submodule` can be used to automatically
# update WebKit and the other submodules
$ git submodule update --init --depth 1 --checkout src/bun.js/WebKit
# to make a jsc release build
$ make jsc
# JSC debug build does not work perfectly with Bun yet, this is actively being
# worked on and will eventually become the default.
$ make jsc-build-linux-compile-debug cpp
$ make jsc-build-mac-compile-debug cpp
```

Note that the WebKit folder, including build artifacts, is 8GB+ in size.

If you are using a JSC debug build and using VScode, make sure to run the `C/C++: Select a Configuration` command to configure intellisense to find the debug headers.

## Troubleshooting

### 'span' file not found on Ubuntu

> ⚠️ Please note that the instructions below are specific to issues occurring on Ubuntu. It is unlikely that the same issues will occur on other Linux distributions.

The Clang compiler typically uses the `libstdc++` C++ standard library by default. `libstdc++` is the default C++ Standard Library implementation provided by the GNU Compiler Collection (GCC). While Clang may link against the `libc++` library, this requires explicitly providing the `-stdlib` flag when running Clang.

Bun relies on C++20 features like `std::span`, which are not available in GCC versions lower than 11. GCC 10 doesn't have all of the C++20 features implemented. As a result, running `make setup` may fail with the following error:

```
fatal error: 'span' file not found
#include <span>
         ^~~~~~
```

To fix the error, we need to update the GCC version to 11. To do this, we'll need to check if the latest version is available in the distribution's official repositories or use a third-party repository that provides GCC 11 packages. Here are general steps:

```bash
$ sudo apt update
$ sudo apt install gcc-11 g++-11
# If the above command fails with `Unable to locate package gcc-11` we need
# to add the APT repository
$ sudo add-apt-repository -y ppa:ubuntu-toolchain-r/test
# Now run `apt install` again
$ sudo apt install gcc-11 g++-11
```

Now, we need to set GCC 11 as the default compiler:

```bash
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 100
$ sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 100
```

### libarchive

If you see an error when compiling `libarchive`, run this:

```bash
$ brew install pkg-config
```

### macOS `library not found for -lSystem`

If you see this error when compiling, run:

```bash
$ xcode-select --install
```

## Arch Linux / Cannot find `libatomic.a`

Bun requires `libatomic` to be statically linked. On Arch Linux, it is only given as a shared library, but as a workaround you can symlink it to get the build working locally.

```bash
$ sudo ln -s /lib/libatomic.so /lib/libatomic.a
```

The built version of Bun may not work on other systems if compiled this way.
