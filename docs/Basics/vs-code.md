---
layout: default
title: Visual Studio Code
parent: Computation Basics
has_children: false
nav_order: 6
has_toc: true
---

# Visual Studio Code Setup

## td;dr
```sh
which code && for extension in $(curl -s https://raw.githubusercontent.com/PennLINC/PennLINC.github.io/master/docs/Basics/vs-code-extension-list.txt); do
code --install-extension $extension
done || echo "in the vscode command pallet (command + shift + p) search for \"Install code command in 'PATH'\""
```

## Remote development

<img style="float: right;max-width: 50%" src="https://microsoft.github.io/vscode-remote-release/images/ssh-readme.gif">

The vscode remote development [extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) lets you open remote directories as if they were local, serving the content through a SSH tunnel that is silently setup in the background. To create a new connection, click the Remote Explorer icon in the sidebar, click "+" next to SSH TARGETS, enter the ssh command you would enter to connect on the command line, and select the SSH configuration file (probably `~/.ssh/config`). Then in the future there will be a shortcut to that SSH target.

More documentation can be found [here](https://code.visualstudio.com/docs/remote/ssh).

## Local development

Remote development is ideal when you're working with lots of data or need the computational power of the cluster, but sometimes local development is easier. If you're primarily using python, install your dependancies with conda and use the [python extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python). If you're using more complex dependancies (like ANTS), containers are a much easier way of ensuring you installed the software the same as it is on the cluster. The vscode container [extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) lets you open editors in containers, much like the remote development extension lets you open windows on the cluster. More documentation can be found [here](https://code.visualstudio.com/docs/remote/containers).

## Running code

If you like clicking buttons or keys instead to run code instead of the command line, [this extension](https://marketplace.visualstudio.com/items?itemName=formulahendry.code-runner) will add these shortcuts:

To run code:
- use shortcut Ctrl+Option+N
- or press F1 and then select/type Run Code,
- or right click the Text Editor and then click Run Code in editor context menu
- or click Run Code button in editor title menu
- or click Run Code button in context menu of file explorer

To stop the running code:
- use shortcut Ctrl+Option+M
- or press F1 and then select/type Stop Code Run
- or right click the Output Channel and then click Stop Code Run in context menu

If you highlight text and right click on it there should be a "run code" option, but if you want to have your text pasted in an existing terminal window you can setup a keyboard shortcut for that by pressing command+k then command+s then searching for workbench.`action.terminal.runSelectedText` then configure it by entering a key combination (I chose `command+ctrl+r`).

You can define arbitrary commands for different languages, for example
```
"code-runner.executorMap": {
   "r": "docker run --rm -i -v $workspaceRoot:$workspaceRoot -w $dir pennsive/neuror:4.1 Rscript $fileName"
}
```

## Troubleshooting
If you have a remote window hanging, you can force the vs code server to close by doing `killall node` on the remote server.

