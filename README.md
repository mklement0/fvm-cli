![project status](https://img.shields.io/badge/status-not_ready_for_release-red.svg)
<!--[![npm version](https://img.shields.io/npm/v/fvm-cli.svg)](https://npmjs.com/package/fvm-cli) [![license](https://img.shields.io/npm/l/fvm-cli.svg)](https://github.com/mklement0/fvm-cli/blob/master/LICENSE.md)-->

# fvm &mdash; VM management CLI for VMware Fusion

`fvm` (*F*usion *V*M *M*anager) is a convenience wrapper around the [`vmrun`  
CLI](https://www.vmware.com/support/developer/vix-api/vix112_vmrun_command.pdf)
that comes with [VMware Fusion](http://www.vmware.com/products/fusion.html) (macOS only).

Its major areas of functionality are:

* VMs can be targeted by display name substrings (regular expressions)  
  instead of having to specify their VMX file path.

* VMs in open windows can be activated or closed, their VMX files can be  
  revealed in Finder or opened for editing, and Fusion can be quit as a whole.

* A list of all registered VMs can be obtained with subcommand `ls`.

As such, `fvm` can make up for some of the functionality that was lost when
AppleScript was quietly removed from VMware Fusion v7.

# Examples

```shell
# Activate (open existing window or start) the VM whose display name
# contains the (unambiguous) substring "w10":
fvm w10     # short for: fvm activate w10

# Activate the VM whose display name contains the words "ubuntu" and "14":
fvm 'ubuntu.*14'

# Close the window of the VM whose display name is exactly "W7 (32-bit)"
fvm -x close "W7 (32-bit)"

# List all registered VMs by display name, guest OS, state, and VMX file path,
# with open VMs listed first.
fvm -s ls

# List VMs whose display names contain the word "ubuntu".
fvm ls 'ubuntu'

## vmrun PASS-THROUGH EXAMPLES
## All examples below use "w10" as the display-name regex for identifying
## the target VM. 

# Suspends a VM
fvm suspend w10

# Checks if a VM has the VMware Tools are installed.
fvm checkToolsState w10

# Lists a VM's snapshots.
fvm listsnapshots w10

# Gets the a VM's (the guest OS's) IP address.
fvm getGuestIpAddress w10

# Runs a program asynchronously and interactively in the guest OS. 
fvm -gu jdoe -gp test runProgramInGuest w10 -nowait -interactive 'C:\WINDOWS\system32\calc.exe'
```

# Installation

**Prerequisites**

* [VMware Fusion](http://www.vmware.com/products/fusion.html) v8.5 (fully tested), _likely_ down to v7 (sporadically tested).
* Any macOS (OS X) version that can run a supported VMware Fusion version.

## Installation from the npm registry

<sup>Note: Even if you don't use Node.js, its package manager, `npm`, works across platforms and is easy to install; try [`curl -L https://git.io/n-install | bash`](https://github.com/mklement0/n-install)</sup>

With [Node.js](http://nodejs.org/) or [io.js](https://iojs.org/) installed, install [the package](https://www.npmjs.com/package/nws-cli) as follows:

    [sudo] npm install fvm-cli -g

**Note**:

* Whether you need `sudo` depends on how you installed Node.js / io.js and whether you've [changed permissions later](https://docs.npmjs.com/getting-started/fixing-npm-permissions); if you get an `EACCES` error, try again with `sudo`.
* The `-g` ensures [_global_ installation](https://docs.npmjs.com/getting-started/installing-npm-packages-globally) and is needed to put `nws` in your system's `$PATH`.

## Manual installation

* Download [the CLI](https://raw.githubusercontent.com/mklement0/fvm-cli/stable/bin/fvm) as `fvm`.
* Make it executable with `chmod +x nws`.
* Move it or symlink it to a folder in your `$PATH`, such as `/usr/local/bin` (OSX) or `/usr/bin` (Linux).

# Usage

Find concise usage information below; for complete documentation, read the [manual online](doc/fws.md) or,
once installed, run `man fvn` (`fvm --man` if installed manually).

<!-- DO NOT EDIT THE FENCED CODE BLOCK and RETAIN THIS COMMENT: The fenced code block below is updated by `make update-readme/release` with CLI usage information. -->

```
$ fvm --help

SYNOPSIS
	fvm [-N]
	fvm -l | -L  [-N]
	fvm [ -r | -s | -S | -c | -v | -t ] [-g -N] vmName
	fvm -R  [-N] vmName

DESCRIPTION
	Manages state of or returns information about VMware Fusion 6 VMs.
	VMs are identified by their display names, as shown in
	VMware Fusion's library window.
	
	1st synopsis form:
		Activates VMWare Fusion and shows the library window.
		To list VMs in the console instead, use -l or -L; see below.

	2nd synopsis form:
		Lists all known VMs, optionally grouped by whether they're open or not.
	
		-l … lists all known VMs by display name in alphabetical order,
		      along with their guest-OS identifier (taken from the *.vmx files).
		-L … lists all known VMs, grouped by whether they're open or not, along
		     with their guest-OS identifier, their power state, *if open [in a window]*, 
		     and the guest IP address, if on or paused (note that it can take a while
		     for a recently started/resumed VM to report its IP address).
		     power-state values are: 'on' / 'paused', 'suspended', 'off'
		-b … uses bare output format for machine parsing: supresses the header
		     and uses tab-separated output fields.	 
		
	3rd synopsis form:
		Acts on a known VMware Fusion VM by display name or substring thereof,
		activating or starting it by default.

		If the targeted VM is currently open, its window is activated.
		Otherwise, it is started.
		Various other operations are available; -g performs them
		in the background, without activating windows.

		Note that the target VM must already be known to VMware Fusion,
		as evidenced by its presence in the list of VMs in the library window.
		Use the names displayed there to target VMs, or any case-insensitive substring
		of these names, as long as it uniquely identifies a known VM; note that
		spaces are ignored when matching.
		You can also invoke this utility with -l to see all known VMs.
		
		-r … ensures that the VM is *resumed* (or started);
			    by default, an open window is merely activated (unless -g is specified), 
			    regardless of the VM's power state.
			    Note that if the VM is not open, it is always started in the powered-on
			    (non-suspended) state.
			
		-s … ensures that the VM is *suspended*; specify -g in addition if you don't want the
			    VM's window, if open, to be activated.

		-S … *toggles suspension* of the specified VM; i.e., if it is currently suspended
			    (or not open), it is resumed (or started); otherwise, it is suspended.

		-p … ensures that the VM is *paused*; use -g in addition, if you don't want the
			    VM's window, if open, to be activated.
			
		-P … *toggles pausing* of the specified VM; i.e., if it is currently running
			    running, it is paused; if it's currently paused, it is unpaused.
					 
		-c … ensures that the target VM's window is *closed* , by default activating it first;
		       whether the VM gets suspended or powered off instantly (discarding its state) - 
		       and whether you're  prompted first - depends on your general VMware Fusion preferences.
		       If you also specify -g, be sure that prompting is turned off, otherwise you may miss prompts.
		       If the target VM is not open, no operation is performed.	
					 
		-v … *toggle* activation - only makes sense if run via global hotkey:
			If the targeted VM is the frontmost window, deactivates it as follows,
			based on what application was frontmost when the window was last *activated*:
			 - if VMware Fusion was frontmost:
			     activates the next VMware Fusion window (the second-most recently used), 
			     if present; otherwise, hides the application.
			 - if another application was frontmost:
			     hides VMware Fusion.
			  Note: Using this option causes state to be maintained in a dedicated
		       	 subfolder of ~/Library/Caches.
						 
		-t … only *tests* whether the specified VM is currently *open in a window*,
			regardless of its power state.
			Sets exit code to 0 if yes, 1 otherwise.
			If, by contrast, you want to know whether a specific VM is *running or paused*,
			use the -L option and look for 'on' or 'paused' in the VM's 'State' column.
			
		-g … performs the operation in the back*g*round; i.e., without activating
		   any windows or VMware Fusion itself; can be combined with any of the options that
		   change the state of a VM; if -g is the only option and the VM is not open, 
		   it is started in the background.
			
		-N … outputs status messages as GUI notifications; by default, they're
		    output to std*err* (rather than stdout - for technical reasons).
			GUI notifications use Growl, if installed. If not, native
			OSX notifications are used on OSX 10.9 and above.
			If neither are available, no status messages are displayed.
			
		Note that access for assistive devices must be enabled (see System Preferences
		> Accessibility); if it is not, an authorization prompt requiring admin credentials
		will display.
			
	4th synopsis form:
		-R reveals the specified VM in Finder; specifically, its *.vmx file.

  -D ... (all synopses) turns on debugging output
		
EXAMPLES
	fvm -l                   # Lists all known VMs and their guest-OS identifiers.
	fm Xp32  	          # Activates or starts the VM whose display name contains "Xp32".
	fvm -c Xp32         # Closes the VM whose display name contains "Xp32".
	fvm -g -S W7U64  # Toggles between suspended and running state of VM "W7U64" without activating its window.
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
* [replace (D)](https://github.com/harthur/replace)
* [semver (D)](https://github.com/npm/node-semver#readme)
* [tap (D)](https://github.com/isaacs/node-tap)
* [urchin (D)](https://github.com/tlevine/urchin)

<!-- DO NOT EDIT THE NEXT CHAPTER and RETAIN THIS COMMENT: The next chapter is updated by `make update-readme/release` with the contents of 'CHANGELOG.md'. ALSO, LEAVE AT LEAST 1 BLANK LINE AFTER THIS COMMENT. -->

## Changelog

* **v0.1.0-0** (2015-06-26):
    * Initial commit.
