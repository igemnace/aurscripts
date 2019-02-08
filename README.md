# AUR Scripts

These are just scripts I use to make building AUR packages more convenient. Use
case is as you would expect: fetch a PKGBUILD by cloning a git repo, then
run makepkg.

## Warning

These scripts have to do with installing packages to your system. Never trust
scripts from the Internet with that purpose unless you have personally vetted
them thoroughly and find that they do what you expect.

I wrote these scripts with a very specific use case in mind: my own use case. I
started with simply doing everything manually, then automated out only the parts
I was comfortable with automating. I take steps to ensure my system's safety,
but this is only apparent in my workflow as a whole. I don't find it hard to
imagine someone else using these exact same scripts and somehow putting their
system at risk simply because of a workflow mismatch.

That said, I see a value in making these scripts public. Perhaps one can learn
to script their own AUR workflow from this repo. Perhaps one can yank out bits
and pieces for their own use. Perhaps one even has a similar workflow to mine,
taking the same care with interacting with the AUR as I do, and so can use these
scripts for convenience.

I leave it to the user's good judgment. **Caveat emptor.**

## Pre-installation

These scripts are designed to work on an Arch Linux system.

The scripts require the following dependencies to work properly:

- bash
- env
- coreutils
- git (for `aurget`, `aurupdate`, and `aurclean`)
- makepkg (for `aurinstall`)
- less (for `aurinstall`)
- curl (for `aurquery` and `aursearch`)
- jq (for `aurquery` and `aursearch`)
- tput (for `aurquery` and `aursearch`)

## Installation

Copy or link the scripts to somewhere in your `PATH`.

## Usage

### Searching for Packages

A simple `aursearch <keyword>` will send a search query to the AUR's RPC
interface.

Hint: it takes a `-q`/`--quiet` flag that acts in the same way as pacman's own
`-q`/`--quiet` flag when running `pacman -Ssq` or `pacman -Qsq`. Fancy example:
`aurget "$(aursearch -q <keyword> | fzf)"` to choose a package to fetch with a
fuzzy finder.

`aurquery <package> ...` will send an info query to the AUR's RPC interface.
It has more detailed output, similar to `pacman -Si` or `pacman -Qi`.

### Installing Packages

`aurget <package> ...` will clone the git repo(s) from the AUR to `$AUR_DIR`
(defaults to `~/.aur`). This ought to fetch the PKGBUILD, to prepare for
installation.

`aurinstall <package> ...` will display the PKGBUILD in a pager for inspection,
then run makepkg. Hint: default pager invocation is `less -K`, so you can press
`q` to quit normally and proceed with installation, or press `ctrl-c` to abort
(e.g. to prevent running a bad PKGBUILD). Also, it's just `less` so usual keys
like `v` to open in an editor still work and can come in handy.

Hint: `aurinstall --asdeps <package> ...` will pass the `--asdeps` flag to
makepkg (which will pass it, in turn, to pacman). This might prove useful in the
case of installing packages only to fulfill dependencies, so they can
automatically be removed by `pacman -Rs`, or properly detected as orphans by
`pacman -Qtd`.

### Maintaining Packages

`aurupdate <package> ...` will simply run `git fetch` in the corresponding git
repo(s), display any new commits on the remote, then attempt a `git merge`. If
any new commits are displayed, a simple `aurinstall <package> ...` can be run to
rebuild the package.

`aurclean <package> ...` will clean up any untracked files such as source
tarballs and built packages, if freeing up disk space is a concern.

### Uninstalling Packages

Packages are installed with the traditional `pacman -U` invocation from makepkg,
so uninstallation is done with `pacman -Rns <package> ...`, as expected.

`aurremove <package> ...` will simply delete the corresponding git repo(s) from
`$AUR_DIR`. No package management operations are run; this is simply for
housekeeping. Hint: this is just `rm -rf` on the repo (`-r` since it's a
directory, `-f` to skip confirmation for various artifacts in `.git`). Tread
lightly. You can easily do something dumb, like `aurremove ../Documents`.

## Other Usage

### Convenience Shell Functions

In some cases, the installation, update, and uninstallation steps are
straightforward enough that almost no manual intervention between each script is
necessary. A few convenience shell functions can really enhance the experience
in these cases: see [this Gist][convenience-functions].

### Shell Completion

I personally use zsh, so I have some simple completion widgets for some of the
scripts: see [this Gist][completion].

## Licensing

This project is free and open source software, licensed under the MIT license.
You are free to use, modify, and redistribute this software.

See `LICENSE` for the full text. Also, take a look at [Github's Choose a License
page][license] for a nice, short explanation.

[license]: https://choosealicense.com/licenses/mit/
[convenience-functions]: https://gist.github.com/igemnace/c5033db29fb79df975d679bc47476dbb
[completion]: https://gist.github.com/igemnace/351dbc1d9cd7a0a3ae100df1c0cf20d5
