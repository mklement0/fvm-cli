[![npm version](https://img.shields.io/npm/v/fvm-cli.svg)](https://npmjs.com/package/fvm-cli) [![license](https://img.shields.io/npm/l/fvm-cli.svg)](https://github.com/mklement0/fvm-cli/blob/master/LICENSE.md)

# fvm &mdash; VM management CLI for VMware Fusion (macOS only)

`fvm` (*F*usion *V*M *M*anager) is a convenience wrapper around the 
[`vmrun` CLI](https://www.vmware.com/support/developer/vix-api/vix112_vmrun_command.pdf)
that comes with [VMware Fusion](http://www.vmware.com/products/fusion.html), 
with added functionality for managing VM window states.

The major areas of functionality are:

* VMs can be targeted by display name substrings (regular expressions)  
  instead of having to specify their VMX file path.

* VMs in open windows can be activated or closed, their VMX files can be  
  revealed in Finder or opened for editing, and Fusion can be quit as a whole.

* A list of all registered VMs can be obtained with subcommand `ls`.

As such, `fvm` can make up for some of the functionality that was lost when
AppleScript support was quietly removed from VMware Fusion v7.

# Examples

```shell
# Show VMware Fusion's Library window.
fvm         # short for: fvm library

# Activate (open existing window or start) the VM whose display name
# contains the (unambiguous) substring "w10":
fvm w10     # short for: fvm activate w10

# Toggle activation of the VM whose display name contains the (unambiguous) 
# substring "w10" (only useful when run via hotkey).
fvm -t w10     # short for: fvm activate w10

# Activate the VM whose display name contains the words "ubuntu" and "14".
fvm 'ubuntu.*14'

# Close the window of the VM whose display name is exactly "W7 (32-bit)".
fvm -x close "W7 (32-bit)"

# List all registered VMs by display name, guest OS, state, and VMX file path,
# with open VMs listed first.
fvm -s ls

# List VMs whose display names contain the word "ubuntu".
fvm ls 'ubuntu'

# Quit VMware Fusion as a whole.
fvm quit

## vmrun PASS-THROUGH EXAMPLES
## All examples below use "w10" as the display-name regex for identifying
## the target VM. 

# Suspend a VM.
fvm suspend w10

# Check if a VM has the VMware Tools are installed.
fvm checkToolsState w10

# List a VM's snapshots.
fvm listsnapshots w10

# Gets a VM's (guest OS's) IP address.
fvm getGuestIpAddress w10

# Run a program asynchronously and interactively in the guest OS. 
fvm -gu jdoe -gp test runProgramInGuest w10 -nowait -interactive 'C:\WINDOWS\system32\calc.exe'
```

# Installation

**Prerequisites**

* [VMware Fusion](http://www.vmware.com/products/fusion.html) v8.5 (fully tested), _likely_ down to v7 (sporadically tested).
* Any macOS (OS X) version that can run a supported VMware Fusion version.

## Installation from the npm registry

<sup>Note: Even if you don't use Node.js, its package manager, `npm`, works across platforms and is easy to install; try [`curl -L https://bit.ly/n-install | bash`](https://github.com/mklement0/n-install)</sup>

With [Node.js](http://nodejs.org/) installed, install [the package](https://www.npmjs.com/package/fvm-cli) as follows:

    [sudo] npm install fvm-cli -g

**Note**:

* Whether you need `sudo` depends on how you installed Node.js / io.js and whether you've [changed permissions later](https://docs.npmjs.com/getting-started/fixing-npm-permissions); if you get an `EACCES` error, try again with `sudo`.
* The `-g` ensures [_global_ installation](https://docs.npmjs.com/getting-started/installing-npm-packages-globally) and is needed to put `fvm` in your system's `$PATH`.

## Manual installation

* Download [the CLI](https://raw.githubusercontent.com/mklement0/fvm-cli/stable/bin/fvm) as `fvm`.
* Make it executable with `chmod +x fvm`.
* Move it or symlink it to a folder in your `$PATH`, such as `/usr/local/bin` (OSX) or `/usr/bin` (Linux).

# Usage

Find concise usage information below; for complete documentation, read the [manual online](doc/fws.md) or,
once installed, run `man fvm` (`fvm --man` if installed manually).

<!-- DO NOT EDIT THE FENCED CODE BLOCK and RETAIN THIS COMMENT: The fenced code block below is updated by `make update-readme/release` with CLI usage information. -->

```nohighlight
$ fvm --help


    fvm      [-t]          [library]
    fvm [-x] [-t]          [activate]   <displayNameRegexOrVmxFile>
    fvm [-x]               close        <displayNameRegexOrVmxFile>
    fvm [-x]               reveal       <displayNameRegexOrVmxFile>
    fvm [-x]               edit         <displayNameRegexOrVmxFile>
    fvm [-x] [-b] [-s]     ls           [<displayNameRegexFilter>]
    fvm                    quit
    fvm [-x] start|pause|unpause|suspend|reset <displayNameRegexOrVmxFile>
    fvm [-x] stop                       <displayNameRegexOrVmxFile> [soft|hard]
      CAVEAT: Append 'soft' to prevent potential VM corruption. 
    fvm [-x] [<auth-opts>] <vmrun-cmd> [...]
    fvm --help-vmrun

    -t ... toggle activation (hotkey-based invocations)
    -x ... display name is specified literally, in full
    -b ... bare, tab-separated output for machine parsing
    -s ... include VM state and show open VMs first
    --help-vmrun ... shows vmrun's command-line help

Standard options: --help, --man, --version, --home
```

<!-- DO NOT EDIT THE NEXT CHAPTER and RETAIN THIS COMMENT: The next chapter is updated by `make update-readme/release` with the contents of 'LICENSE.md'. ALSO, LEAVE AT LEAST 1 BLANK LINE AFTER THIS COMMENT. -->

## License

Copyright (c) 2016 Michael Klement, released under the [MIT license](http://opensource.org/licenses/MIT).

### Acknowledgements

This project gratefully depends on the following open-source components, according to the terms of their respective licenses.

[npm](https://www.npmjs.com/) dependencies below have optional suffixes denoting the type of dependency; the absence of a suffix denotes a required run-time dependency: `(D)` denotes a development-time-only dependency, `(O)` an optional dependency, and `(P)` a peer dependency.

<!-- DO NOT EDIT THE NEXT CHAPTER and RETAIN THIS COMMENT: The next chapter is updated by `make update-readme/release` with the dependencies from 'package.json'. ALSO, LEAVE AT LEAST 1 BLANK LINE AFTER THIS COMMENT. -->

### npm dependencies

* [json (D)](https://github.com/trentm/json)
* [marked-man (D)](https://github.com/kapouer/marked-man#readme)
* [replace (D)](https://github.com/harthur/replace)
* [semver (D)](https://github.com/npm/node-semver#readme)
* [tap (D)](https://github.com/isaacs/node-tap)
* [urchin (D)](https://github.com/tlevine/urchin)

<!-- DO NOT EDIT THE NEXT CHAPTER and RETAIN THIS COMMENT: The next chapter is updated by `make update-readme/release` with the contents of 'CHANGELOG.md'. ALSO, LEAVE AT LEAST 1 BLANK LINE AFTER THIS COMMENT. -->

## Changelog


* **v0.1.0** (2016-12-18):
  * Initial release.
