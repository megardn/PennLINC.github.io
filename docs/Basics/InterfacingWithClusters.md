---
layout: default
title: Working on CUBIC/PMACS via Visual Code Studio
parent: Computation Basics
nav_order: 3
has_toc: true
---

# Interfacing with the clusters

## Prerequisite
You will need ssh keys set up, a PMACS or CUBIC account (with VPN).

https://pennlinc.github.io/docs/Basics/sshKeys/

## General Principles & Motivation

1. We want to code interactively with zero lag in a format that encourages documenting your code
2. We want the software and data we are using to be on the cluster, preventing version issues or having to download a test set to your local machine
3. We want it to be easy!

This means we are going to not use X11 at all. Why? Because running graphics on the cluster, and then having them sent to your local screen, is very laggy and not dependable.

## Download/Install Visual Code Studio

You can find it here: https://code.visualstudio.com

Remote Development using SSH
============================

The Visual Studio Code Remote - SSH extension allows you to open a remote folder on any remote machine, virtual machine, or container with a running SSH server and take full advantage of VS Code's feature set. Once connected to a server, you can interact with files and folders anywhere on the remote filesystem.

No source code needs to be on your local machine to gain these benefits since the extension runs commands and other extensions directly on the remote machine.

![SSH Architecture](https://code.visualstudio.com/assets/docs/remote/ssh/architecture-ssh.png)

This lets VS Code provide a local-quality development experience --- including full IntelliSense (completions), code navigation, and debugging --- regardless of where your code is hosted.

Getting started[#](https://code.visualstudio.com/docs/remote/ssh#_getting-started)
----------------------------------------------------------------------------------

Note: After reviewing this topic, you can get started with the introductory [SSH tutorial](https://code.visualstudio.com/docs/remote/ssh-tutorial).

### System requirements[#](https://code.visualstudio.com/docs/remote/ssh#_system-requirements)

Local: A supported [OpenSSH compatible SSH client](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) must also be installed.

Remote SSH host: A running [SSH server](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-server) on:

-   x86_64 Debian 8+, Ubuntu 16.04+, CentOS / RHEL 7+.
-   ARMv7l (AArch32) Raspbian Stretch/9+ (32-bit).
-   ARMv8l (AArch64) Ubuntu 18.04+ (64-bit).
-   Windows 10 / Server 2016/2019 (1803+) using the [official OpenSSH Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse).
-   macOS 10.14+ (Mojave) SSH hosts with [Remote Login enabled](https://support.apple.com/guide/mac-help/allow-a-remote-computer-to-access-your-mac-mchlp1066/mac).

Other `glibc` based Linux distributions for x86_64, ARMv7l (AArch32), and ARMv8l (AArch64) should work if they have the needed prerequisites. See the [Remote Development with Linux](https://code.visualstudio.com/docs/remote/linux) article for information prerequisites and tips for getting community supported distributions up and running.

While ARMv7l (AArch32) and ARMv8l (AArch64) support is available, some extensions installed on these devices may not work due to the use of x86 native code in the extension.

### Installation[#](https://code.visualstudio.com/docs/remote/ssh#_installation)

To get started, you need to:

1.  Install an [OpenSSH compatible SSH client](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) if one is not already present.

2.  Install [Visual Studio Code](https://code.visualstudio.com/) or [Visual Studio Code Insiders](https://code.visualstudio.com/insiders/).

3.  Install the [Remote Development extension pack](https://aka.ms/vscode-remote/download/extension).

### SSH host setup[#](https://code.visualstudio.com/docs/remote/ssh#_ssh-host-setup)

1.  If you do not have an SSH host set up, follow the directions for [Linux](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-server), [Windows 10 / Server (1803+)](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse), or [macOS](https://support.apple.com/guide/mac-help/allow-a-remote-computer-to-access-your-mac-mchlp1066/mac) SSH host or create a [VM on Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

2.  [Optional] If your Linux or macOS SSH host will be accessed by multiple users at the same time, consider enabling Remote.SSH: Remote Server Listen On Socket in VS Code [User settings](https://code.visualstudio.com/docs/getstarted/settings) for improved security.

    In the Settings editor:

    ![Listen on socket VS Code setting](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-listen-on-socket.png)

    See the [Tips and Tricks](https://code.visualstudio.com/docs/remote/troubleshooting#_improving-security-on-multi-user-servers) article for details.

3.  [Optional] While password-based authentication is supported, we recommend setting up key based authentication for your host. See the [Tips and Tricks](https://code.visualstudio.com/docs/remote/troubleshooting#_configuring-key-based-authentication) article for details.

### Connect to a remote host[#](https://code.visualstudio.com/docs/remote/ssh#_connect-to-a-remote-host)

To connect to a remote host for the first time, follow these steps:

1.  Verify you can connect to the SSH host by running the following command from a terminal / PowerShell window replacing `user@hostname` as appropriate.

    ```
    ssh user@hostname
    # Or for Windows when using a domain / AAD account
    ssh user@domain@hostname
    ```

2.  In VS Code, select Remote-SSH: Connect to Host... from the Command Palette (F1) and use the same `user@hostname` as in step 1.

    ![Illustration of user@host input box](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-user@box.png)

3.  If VS Code cannot automatically detect the type of server you are connecting to, you will be asked to select the type manually.

    ![Illustration of platform selection](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-select-platform.png)

    Once you select a platform, it will be stored in [VS Code settings](https://code.visualstudio.com/docs/getstarted/settings) under the `remote.SSH.remotePlatform` property so you can change it at any time.

4.  After a moment, VS Code will connect to the SSH server and set itself up. VS Code will keep you up-to-date using a progress notification and you can see a detailed log in the `Remote - SSH` output channel.

    > Tip: Connection hanging or failing? See [troubleshooting tips](https://code.visualstudio.com/docs/remote/troubleshooting#_troubleshooting-hanging-or-failing-connections) for information on resolving common problems.
    >
    > If you see errors about SSH file permissions, see the section on [Fixing SSH file permission errors](https://code.visualstudio.com/docs/remote/troubleshooting#_fixing-ssh-file-permission-errors).

5.  After you are connected, you'll be in an empty window. You can always refer to the Status bar to see which host you are connected to.

    ![SSH Status bar item](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-statusbar.png)

    Clicking on the Status bar item will provide a list of remote commands while you are connected.

6.  You can then open any folder or workspace on the remote machine using File > Open... or File > Open Workspace... just as you would locally!

    ![File Open on a remote SSH host](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-open-folder.png)

From here, [install any extensions](https://code.visualstudio.com/docs/remote/ssh#_managing-extensions) you want to use when connected to the host and start editing!

> Note: On ARMv7l / ARMv8l `glibc` SSH hosts, some extensions may not work due to x86 compiled native code inside the extension.

### Disconnect from a remote host[#](https://code.visualstudio.com/docs/remote/ssh#_disconnect-from-a-remote-host)

To close the connection when you finish editing files on the remote host, choose File > Close Remote Connection to disconnect from the host. The default configuration does not include a keyboard shortcut for this command. You can also simply exit VS Code to close the remote connection.

### Remember hosts and advanced settings[#](https://code.visualstudio.com/docs/remote/ssh#_remember-hosts-and-advanced-settings)

If you have a set of hosts you use frequently or you need to connect to a host using some additional options, you can add them to a local file that follows the [SSH config file format](https://man7.org/linux/man-pages/man5/ssh_config.5.html).

To make setup easy, the extension can guide you through adding a host without having to hand edit this file.

Start by selecting Remote-SSH: Add New SSH Host... from the Command Palette (F1) or clicking on the Add New icon in the SSH Remote Explorer in the Activity Bar.

![Remote Explorer Add New item](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-explorer-add-new.png)

You'll then be asked to enter the SSH connection information. You can either enter a host name:

![Remote Explorer SSH host input](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-host-input.png)

Or the full `ssh` command you would use to connect to the host from the command line:

![Remote Explorer SSH command input](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-command-input.png)

Finally, you'll be asked to pick a config file to use. You can also set the `"remote.SSH.configFile"`property in your User `settings.json` file if you want to use a different config file than those listed. The extension takes care of the rest!

For example, entering `ssh -i ~/.ssh/id_rsa-remote-ssh yourname@remotehost.yourcompany.com` in the input box would generate this entry:

```
Host remotehost.yourcompany.com
    User yourname
    HostName another-host-fqdn-or-ip-goes-here
    IdentityFile ~/.ssh/id_rsa-remote-ssh

```

See [Tips and Tricks](https://code.visualstudio.com/docs/remote/troubleshooting#_improving-your-security-with-a-dedicated-key) for details on generating the key shown here. You can manually edit this file with anything the [SSH config file format](https://man7.org/linux/man-pages/man5/ssh_config.5.html) supports, so this is just one example.

From this point forward, the host will appear in the list of hosts when you select Remote-SSH: Connect to Host... from the Command Palette (F1) or in the SSH Targets section of the Remote Explorer.

![SSH targets in the Remote Explorer](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-explorer-connect.png)

The Remote Explorer allows you to both open a new empty window on the remote host or directly open a folder you previously opened. Expand the host and click on the Open Folder icon next to the folder you want to open on the host.

![Remote Explorer open folder](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-explorer-open-folder.png)

Managing extensions[#](https://code.visualstudio.com/docs/remote/ssh#_managing-extensions)
------------------------------------------------------------------------------------------

VS Code runs extensions in one of two places: locally on the UI / client side, or remotely on the SSH host. While extensions that affect the VS Code UI, like themes and snippets, are installed locally, most extensions will reside on the SSH host. This ensures you have smooth experience and allows you to install any needed extensions for a given workspace on an SSH host from your local machine. This way, you can pick up exactly where you left off, from a different machine complete with your extensions.

If you install an extension from the Extensions view, it will automatically be installed in the correct location. Once installed, you can tell where an extension is installed based on the category grouping.

There will be a category for your remote SSH host:

![Workspace Extension Category](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-installed-remote-indicator.png)

And also a Local - Installed category:

![Local Extension Category](https://code.visualstudio.com/assets/docs/remote/common/local-installed-extensions.png)

> Note: If you are an extension author and find that your extension is not working properly or installs in the wrong place, see [Supporting Remote Development](https://code.visualstudio.com/api/advanced-topics/remote-extensions) for details.

Local extensions that actually need to run remotely will appear dimmed and disabled in the Local - Installed category. Select Install to install an extension on your remote host.

![Disabled Extensions w/Install Button](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-disabled-extensions.png)

You can also install all locally installed extensions on the SSH host by going to the Extensions view and selecting Install Local Extensions in SSH: [Hostname] using the cloud button at the right of the Local - Installed title bar. This will display a dropdown where you can select which locally installed extensions to install on your SSH host.

### "Always installed" extensions[#](https://code.visualstudio.com/docs/remote/ssh#_always-installed-extensions)

If there are extensions that you would like to always have installed on any SSH host, you can specify which ones using the `remote.SSH.defaultExtensions` property in `settings.json`. For example, if you wanted to install the [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) and [Resource Monitor](https://marketplace.visualstudio.com/items?itemName=mutantdino.resourcemonitor) extensions, specify their extension IDs as follows:

```
"remote.SSH.defaultExtensions": [
    "eamodio.gitlens",
    "mutantdino.resourcemonitor"
]
```

### Advanced: Forcing an extension to run locally / remotely[#](https://code.visualstudio.com/docs/remote/ssh#_advanced-forcing-an-extension-to-run-locally-remotely)

Extensions are typically designed and tested to either run locally or remotely, not both. However, if an extension supports it, you can force it to run in a particular location in your `settings.json` file.

For example, the setting below will force the Docker extension to run locally and Debugger for Chrome extension to run remotely instead of their defaults:

```
"remote.extensionKind": {
    "ms-azuretools.vscode-docker": [ "ui" ],
    "msjsdiag.debugger-for-chrome": [ "workspace" ]
}
```

A value of `"ui"` instead of `"workspace"` will force the extension to run on the local UI/client side instead. Typically, this should only be used for testing unless otherwise noted in the extension's documentation since it can break extensions. See the article on [Supporting Remote Development](https://code.visualstudio.com/api/advanced-topics/remote-extensions) for details.

Forwarding a port / creating SSH tunnel[#](https://code.visualstudio.com/docs/remote/ssh#_forwarding-a-port-creating-ssh-tunnel)
--------------------------------------------------------------------------------------------------------------------------------

Sometimes when developing, you may need to access a port on a remote machine that is not publicly exposed. There are two ways to do this using an [SSH tunnel](https://www.ssh.com/ssh/tunneling/example) that "forwards" the desired remote port to your local machine.

### Temporarily forwarding a port[#](https://code.visualstudio.com/docs/remote/ssh#_temporarily-forwarding-a-port)

Once you are connected to a host, if you want to temporarily forward a new port for the duration of the session, select Forward a Port from the Command Palette (F1) or click on the Forward New Port icon in the Remote Explorer after selecting it from the Activity Bar.

![Remote Explorer forward port button](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-explorer-forward-port.png)

You'll be asked to enter the port you would like to forward and you can give it a name.

![Forward port input](https://code.visualstudio.com/assets/docs/remote/ssh/forward-port-ssh.png)

A notification will tell you the localhost port you should use to access the remote port. For example, if you forwarded an HTTP server listening on port 3000, the notification may tell you that it was mapped to port 4123 on localhost since 3000 was already in use. You can then connect to this remote HTTP server using `http://localhost:4123`.

This same information is available in the Forwarded Ports section of the Remote Explorer if you need to access it later.

If you would like VS Code to remember any ports you have forwarded, check Remote: Restore Forwarded Ports in the Settings editor (⌘,) or set `"remote.restoreForwardedPorts": true` in `settings.json`.

![Restore forwarded ports setting](https://code.visualstudio.com/assets/docs/remote/common/restore-forwarded-ports.png)

#### CHANGE LOCAL PORT ON TUNNEL

If you would like the local port of the tunnel to be different than the remote server's, you can change this via the Forwarded Ports panel.

Right-click the tunnel you want to modify, and select Change Local Port in the context menu.

![Change Local Port](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-tunnel-different-local-port.png)

### Always forwarding a port[#](https://code.visualstudio.com/docs/remote/ssh#_always-forwarding-a-port)

If you have ports that you always want to forward, you can use the `LocalForward` directive in the same SSH config file you use to [remember hosts and advanced settings](https://code.visualstudio.com/docs/remote/ssh#_remember-hosts-and-advanced-settings).

For example, if you wanted to forward ports 3000 and 27017, you could update the file as follows:

```
Host remote-linux-machine
    User myuser
    HostName remote-linux-machine.mydomain
    LocalForward 127.0.0.1:3000 127.0.0.1:3000
    LocalForward 127.0.0.1:27017 127.0.0.1:27017

```

Opening a terminal on a remote host[#](https://code.visualstudio.com/docs/remote/ssh#_opening-a-terminal-on-a-remote-host)
--------------------------------------------------------------------------------------------------------------------------

Opening a terminal on the remote host from VS Code is simple. Once connected, any terminal windowyou open in VS Code (Terminal > New Terminal) will automatically run on the remote host rather than locally.

You can also use the `code` command line from this same terminal window to perform a number of operations such as opening a new file or folder on the remote host. Type `code --help` to see all the options available from the command line.

![Using the code CLI](https://code.visualstudio.com/assets/docs/remote/ssh/code-command-in-terminal.png)

Debugging on the SSH host[#](https://code.visualstudio.com/docs/remote/ssh#_debugging-on-the-ssh-host)
------------------------------------------------------------------------------------------------------

Once you are connected to a remote host, you can use VS Code's debugger in the same way you would when running the application locally. For example, if you select a launch configuration in `launch.json`and start debugging (F5), the application will start on remote host and attach the debugger to it.

See the [debugging](https://code.visualstudio.com/docs/editor/debugging) documentation for details on configuring VS Code's debugging features in `.vscode/launch.json`.

SSH host-specific settings[#](https://code.visualstudio.com/docs/remote/ssh#_ssh-hostspecific-settings)
-------------------------------------------------------------------------------------------------------

VS Code's local User settings are also reused when you are connected to an SSH host. While this keeps your user experience consistent, you may want to vary some of these settings between your local machine and each host. Fortunately, once you have connected to a host, you can also set host-specific settings by running the Preferences: Open Remote Settings command from the Command Palette (F1) or by selecting on the Remote tab in the Settings editor. These will override any User settings you have in place whenever you connect to the host. And Workspace settings will override Remote and User settings.

![Host-specific settings tab](https://code.visualstudio.com/assets/docs/remote/ssh/ssh-settings.png)
