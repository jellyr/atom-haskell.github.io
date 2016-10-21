---
layout:page
date:2016-10-21 22:00:00
category:doc
order:0
---

# haskell-ghc-mod atom package

This package is primarily intended as backend for [ide-haskell](https://atom.io/packages/ide-haskell).

Haskell ghc-mod opens pipe to ghc-modi and queries types, info and checks
for errors. It uses temporary files to feed them into ghc-mod (since it does
not read from stdin)

If ghc-mod/ghc-modi is not in your PATH, set full path to those in config.

## Dependencies

NOTE: when using with stack, see https://github.com/atom-haskell/haskell-ghc-mod/wiki/Using-with-stack

You need to have `ghc-mod`, `ghc-modi` (part of ghc-mod) and `hlint` executables
installed on your system. `ghc-mod` needs to be able to find `hlint` (eiter add `hlint` directory to PATH, or install both in the same cabal sandbox).

Supported `ghc-mod` versions are 5.5.x. and up. 5.4 is deprecated, but should work in most cases. Earlier versions most likely won't work.

Quick setup is as follows:

```
$ cabal update
$ cabal install ghc-mod
```

After this process finishes, you'll have `ghc-mod`, `ghc-modi` and `hlint` available in `$HOME/.cabal/bin/` directory.

Please note, that for ghc>=7.10 and/or cabal>=1.22, you need ghc-mod>=5.3.0.0. ghc-mod versions before 5.3.0.0 won't work.

User interface is provided by [ide-haskell](https://atom.io/packages/ide-haskell)

## Atom Linter package support

haskell-ghc-mod can use [linter](https://atom.io/packages/linter) package instead of ide-haskell to show check and lint results. You still need ide-haskell for type/info tooltips though.

To use linter, enable 'Use Linter' option in haskell-ghc-mod settings. Bear in mind, that it will disable ide-haskell markers for check/lint results. As of now, no additional checks are preformed, so if linter package is not installed or disabled, you won't see check/lint results at all.

## Installation

```
$ apm install language-haskell haskell-ghc-mod ide-haskell autocomplete-haskell
```

## Configuration

NOTE: when using with stack, see https://github.com/atom-haskell/haskell-ghc-mod/wiki/Using-with-stack

Only configuration option you will likely need to set is `ghcModPath`. It needs to be set to full path to `ghc-mod` executable, if it is not in your PATH. For example, if you have `ghc-mod` in `/home/user/.cabal/bin/`, you need to write `/home/user/.cabal/bin/ghc-mod` in `ghcModPath`. Note that shell expansions are *not* suported, i.e. you can't use `~` or `$HOME`.

There can be some problems with ghc-modi upstream, most notably, it does not
work on paths with whitespace. If you experience problems, try disabling
`ghc-modi` by setting `enableGhcModi` to `false` (or uncheck tick in settings).
This will be slower, but may work better on some configurations.

If you are on OSX, or have ghc installed with non-standard prefix, you may also
consider adding path to directory containing ghc/ghci executable to
`additionalPathDirectories` configuration option. It is a comma-separated list
of directories that will be added to your search path when invoking ghc-mod.
For example, if you have ghc installed to `/usr/local`, then you would add
`/usr/local/bin` to `additionalPathDirectories`.

On OSX, if you start Atom from Finder or with desktop icon, it doesn't inherit
environment variables specified in your user shell (in `.profile`, `.bashrc`,
etc). You can copy `PATH` settings from your shell to
`additionalPathDirectories`, if you'd like to run Atom in this way.

## Keybindings

Haskell-ghc-mod comes with little pre-specified keybindings, so you will need to specify your own, if you want those.

You can edit Atom keybindings by opening 'Edit → Open Your Keymap'. Here is a template for all commands, provided by haskell-ghc-mod:

```cson
'atom-text-editor[data-grammar~="haskell"]':
  '': 'haskell-ghc-mod:check-file'
  '': 'haskell-ghc-mod:lint-file'
  'ctrl-alt-t': 'haskell-ghc-mod:show-type' #this is an example binding
  'ctrl-alt-i': 'haskell-ghc-mod:show-info' #this is an example binding
  'ctrl-alt-T': 'haskell-ghc-mod:insert-type' #this is an example binding
  '': 'haskell-ghc-mod:case-split'
  '': 'haskell-ghc-mod:sig-fill'
  '': 'haskell-ghc-mod:show-info-fallback-to-type'
  '': 'haskell-ghc-mod:show-type-fallback-to-info'
  '': 'haskell-ghc-mod:show-type-and-info'
  '': 'haskell-ghc-mod:insert-import'
  '': 'haskell-ghc-mod:go-to-declaration'

'atom-workspace':
  '': 'haskell-ghc-mod:shutdown-backend'
```

## Service-hub API

Since 1.0.0, haskell-ghc-mod provides `haskell-completion-backend` service.

**NOTE**: Prior to 1.0.0, ide-backend service was provided. It has been scrapped in favor of ide-haskell's UPI.

You can find description in [completion-backend.coffee][2]

[2]:https://github.com/atom-haskell/haskell-ghc-mod/blob/master/lib/completion-backend/completion-backend.coffee

# Advanced configuration

In some cases, it could be useful to disable ghc-mod completely for a given project (e.g. GHCJS), or suppress error pop-ups (e.g. in case of known ghc-mod bugs where some features don't work, or don't always work).

You can create `.haskell-ghc-mod.json` file in project root (i.e. directory containing a `*.cabal` file, or -- in case of plain projects -- Atom's project root directory).

You can also create a global config file in `${ATOM_CONFIG_DIR}/haskell-ghc-mod.json`. `${ATOM_CONFIG_DIR}` is usually `${HOME}/.atom`, but you can check it's path by running `atom.getConfigDirPath()` in Atom's developer console (View → Developer → Toggle Developer Tools → Console).

Config file is a JSON file with the following fields:

- `"disable"` -- `true`/`false`. Will disable all ghc-mod functions entirely. If omitted, defaults to `false`.
- `"suppressErrors"` -- `true`/`false`. Will suppress error pop-ups. Those still will be displayed in Atom's console (View → Developer → Toggle Developer Tools), so if someting seems wierd, one could check there.
- `"ghcOptions"` -- Array of Strings. Options to pass to GHC. Can be useful to explicitly suppress warnings, e.g. `-fno-warn-unused-do-bind` or anything else.
- `"ghcModOptions"` -- Array of Strings. Arbitrary options to pass to ghc-mod. Bear in mind that you shouldn't *really* change most ghc-mod options, since the package makes some assumptions on that part. Also only global ghc-mod options will work (i.e. no command-specific ones)

Example:

```json
{
  "disable": false,
  "suppressErrors": true,
  "ghcOptions": ["-fno-warn-unused-do-bind", "-fno-warn-name-shadowing"],
  "ghcModOptions": ["--with-ghc", "/path/to/custom/ghc"]
}
```

# Using with stack

Stack support is limited. This page contains some tricks that can help to coerce ghc-mod into cooperation.

## Remove `dist` folder in project root

Ghc-mod assumes you want to use cabal if it finds `dist/setup-config` in project root. In any case, if you want to use stack, you don't need `dist` directory anyway (unless you're using it to store sources, in which case, it's strongly advised you don't)

## Maintain a separate ghc-mod installation for each stack resolver

Ghc-mod requires that it must be built with the same version of GHC that you use to build your project. In most cases this means that you need a separate ghc-mod installation for every distinct stack resolver you're using.

Simplest way to achieve this would be to install ghc-mod locally for every stack project you want to use it with, i.e. run `stack build ghc-mod` in project directory (if you have multiple `stack.yaml` configs, do this for each one). If this fails for some reason, try to use stack-installed ghc (with `stack --no-system-ghc --install-ghc build ghc-mod`). Note that some resolvers seemingly can't build ghc-mod at all. You might also want to install newer ghc-mod version than a given resolver offers. Refer to stack documentation on how exactly you could do that.

If you enable 'Stack Sandbox' in haskell-ghc-mod settings (enabled by default), and leave 'Ghc Mod Path' as default `ghc-mod` (i.e. no actual path), Atom should automatically pick up local stack installation.

With lts-4.1 resolver (and probably later), you can also install ghc-mod into 'global project' (essentially run `stack build ghc-mod` outside any project directory). This will automatically provide ghc-mod executables to all projects using this resolver (so you don't need to install it per-project). Note this does *not* work with lts-3.22.

Later stack versions (and resolvers) install packages per-resolver globally, regardless of if you run `stack build ghc-mod` in stack project directory or not. Not sure which version changed that, but stack 1.0.2 with lts-5.6 resolver does that.

## Avoid mixing stack- and cabal-installed packages

This should go without saying. It's always a bad idea to mix packages installed with stack and cabal-install. At least avoid mixing those in the same project, i.e. if you're using stack, use stack-installed ghc-mod. If you're using cabal-install, use cabal-installed ghc-mod. Yes, it is not exactly simple.

## Run Atom with `stack exec atom` (***deprecated***)

***Should not be required with haskell-ghc-mod 1.6.0 and up. Please create issue if it doesn't work***

***This can lead to ghc-mod complaining about GHC_PACKAGE_PATH, so avoid this workaround if at all possible***

Stack manages multiple ghc installations using environment hacking. So, in order to bring this environment into scope when using Atom, the most straightforward way is to run Atom with `stack exec atom` from project directory.
Under Windows it will be something like: `stack exec "%USERPROFILE%\AppData\Local\atom\app-1.7.3\atom.exe"`

DO NOT specify full path to ghc-mod/ghc-modi in haskell-ghc-mod settings. Do not add anything to 'Additional Path Directories' as well, unless you need to (i.e. know what you're doing).

# License

This software is licensed under MIT license. See LICENSE.md for details.

Contributors:

* Nikolay Yakimov
* Daniel Gröber
* Petr Gladkikh
* Mike MacDonald
* Maiddog
* Jason Jackson
* Dennis J. McWherter Jr
* Aaron Wolf
