<img src="https://raw.githubusercontent.com/b4b4r07/screenshots/master/zplug/logo.png" height="150" alt="vim-plug">
===

[repo]: https://github.com/b4b4r07/zplug

`zplug` is a next-generation plugin manager for zsh

[![](https://raw.githubusercontent.com/b4b4r07/screenshots/master/zplug/demo.gif)][repo]

## Pros.

- Manage everything
- Super-fast parallel installation/update
- Branch/tag/commit support
- Can manage UNIX commands (what language is okay)
- Post-update hooks
- Support for externally managed plugins like oh-my-zsh
- Can manage binaries (e.g., GitHub Releases)
- Creates shallow clones to minimize disk space usage and download time
- Understand dependencies between plugins

## Installation

Download [zplug](https://git.io/zplug) and put it in `~/.zplug`

```console
$ curl -fLo ~/.zplug/zplug --create-dirs git.io/zplug
```

## Usage

Add a zplug section to your `.zshrc`:

1. List the plugins/commands with `zplug` commands
2. `zplug load` to source the plugins and add its commands to your `$PATH`

### Example

```zsh
source ~/.zplug/zplug

# Make sure you use double quotes
zplug "zsh-users/zsh-syntax-highlighting"
zplug "zsh-users/zsh-history-substring-search"

# Can manage a plugin as a command
zplug "junegunn/dotfiles", as:cmd, of:bin/vimcat

# Manage everything e.g. zshrc (alias)
zplug "tcnksm/docker-alias", of:zshrc

# Prohibit updates to a plugin by using the frozen keyword
zplug "k4rthik/git-cal", as:cmd, frozen:1

# Grab binaries (from GitHub Releases)
zplug "junegunn/fzf-bin", \
    as:cmd, \
    from:gh-r, \
    file:fzf

# Run a command after a plugin is installed
zplug "tj/n", do:"make install"

# Support checking out a specific branch/tag/commit of a plugin
zplug "b4b4r07/enhancd", at:v1
zplug "mollifier/anyframe", commit:4c23cb60

# Install if `if` specifier returns true
zplug "hchbaw/opp.zsh", if:"(( ${ZSH_VERSION%%.*} < 5 ))"

# Group dependencies, emoji-cli depends on jq in this example
zplug "stedolan/jq", \
    as:cmd, \
    file:jq, \
    from:gh-r \
    | zplug "b4b4r07/emoji-cli"

# Install plugins if there are plugins that have not been installed
if ! zplug check --verbose; then
    printf "Install? [y/N]: "
    if read -q; then
        echo; zplug install
    fi
fi

# Then, source plugins and add commands to $PATH
zplug load
```

Finally, use `zplug install` to install your plugins and reload `.zshrc`.

### `zplug` commands

|  Command  | Description | Option |
|-----------|-------------|--------|
| `install` | Install described items (plugins/commands) in parallel | `--verbose` |
| `load`    | Load installed items | N/A |
| `list`    | List installed items | N/A |
| `update`  | Update items in parallel | `--self` |
| `check`   | Check whether an installation is available | `--verbose`,`--install` |
| `status`  | Check if the remote is up-to-date | N/A |

In detail:

```zsh
# zplug check return true if all plugins are installed
# Therefore, when it returns not true (thus false),
# run zplug install
if ! zplug check; then
    zplug install
fi

# source and add to the PATH
zplug load

# zplug check returns true if argument repository exists
if zplug check b4b4r07/enhancd; then
    # setting if enhancd is available
    export ENHANCD_FILTER=fzf-tmux
fi
```

[![](https://raw.githubusercontent.com/b4b4r07/screenshots/master/zplug/update.gif)][repo]

### `zplug` specifiers

| Specifier | Description | Value (default) | Example |
|-----------|-------------|-----------------|---------|
| `as`      | Specify whether to register as commands or to register as plugins | `src`,`cmd` (`src`) | `as:cmd` |
| `of`      | Specify the pattern to source files (for `src`) or specify relative path to add to the `$PATH` (for `cmd`) | - (-) | `of:bin`,`of:*.zsh` |
| `from`    | Grab external binaries from e.g., GitHub Releases | `gh-r` (-) | `from:gh-r` |
| `at`      | Support branch/tag installation | branch/tag name (`master`) | `at:v1.5.6` |
| `file`    | Specify filename you want to rename | filename (-) | `file:fzf` |
| `dir`     | Installation directory | **READ ONLY** | -
| `if`      | Specify the conditions under which to perform (`source`, add `$PATH`) | true/false (-) | `if:"[ -d ~/.zsh ]"` |
| `do`      | Run commands after installation | commands (-) | `do:make` |
| `frozen`  | Do not update unless explicitly specified | 0,1 (0) | `frozen:1` |
| `commit`  | Support commit installation (regardless of whether the `$ZPLUG_SHALLOW` is true or not) | commit hash (-) | `commit:4428d48` |
| `on`      | Dependencies | **READ ONLY** | - |

### `zplug` configurations

#### `ZPLUG_HOME`

Default value is `~/.zplug`.

`zplug` will store/load plugins in this directory. The directory structure is below.

```
$ZPLUG_HOME
|-- bin
|   `-- some_command -> ../repos/username_A/reponame1/some_command
`-- repos
    |-- username_A
    |   |-- reponame1
    |   |   |-- README.md
    |   |   `-- some_command
    |   `-- reponame2
    |       |-- README.md
    |       `-- some_plugin.zsh
    `-- username_B
        `-- reponame1
```

If you specify `as:cmd` in `zplug` command, zplug will recognize the plugin as a command and create a symbolic link of the same name (if you want to rename it, set `file:` specifier) within `$ZPLUG_HOME/bin`. Because zplug adds `$ZPLUG_HOME/bin` to the `$PATH`, you can run that command from any directories.

#### `ZPLUG_THREADS`

The number of threads zplug should use. The default value is 16.

#### `ZPLUG_PROTOCOL`

Defaults to HTTPS. Valid options for `$ZPLUG_PROTOCOL` are HTTPS or SSH. Unless you have a specific reason, you should use the HTTPS protocol.

For more information, see also [**Which remote URL should I use?** - GitHub Help](https://help.github.com/articles/which-remote-url-should-i-use/)

#### `ZPLUG_SHALLOW`

Defaults to `true`. Makes zplug use shallow clone with a history truncated to the specified number of revisions (depth 1).

## Note

- :tada: Released Beta version!!
- :construction: Until version 1.0.0 is released, `zplug` may be changed in ways that are not backward compatible.
- :hibiscus: It was heavily inspired by [vim-plug](https://github.com/junegunn/vim-plug) and the like.

## Other resources

[awesome-zsh-plugins](https://github.com/unixorn/awesome-zsh-plugins) is a list of ZSH plugins, themes and completions that you can use with zplug.

## License

[MIT][license] © BABAROT

[license]: http://b4b4r07.mit-license.org
