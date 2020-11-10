---
layout: post
title: Use a Docker container as a full-featured development environment
---
Have you ever wanted to try a new programming language but gave up because of all the prerequisites you need to install? Or you wanted to make a small change in a (old) project only to find out that you need another version of the programming language than the one currently installed on your PC? With **development containers** (or **dev containers**), this can be something of the past.  
According to the [documentation](https://code.visualstudio.com/docs/remote/containers), a dev container is a Docker container, setup to run an application or to sandbox tools, libraries, or runtimes needed for working with a codebase. The Visual Studio Code **Remote - Containers** extension makes it easy to setup a dev container and use it as a full-featured development environment.
Once setup, you can store the configuration files (container definition) with the other project files, so that whenever needed you can recreate the same development environment using the container definition.

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Remote - Container](vscode:extension/ms-vscode-remote.remote-containers) VS Code extension

## Setup a dev container for a new project

In this paragraph we will setup a dev container to write code in Python 3:

- Start Visual Studio Code
- Open the Command Palette with <kbd>F1</kbd> or <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd>
- Enter `remote container` and select `Remote Containers: Open Folder in Container...`
  ![Remote Containers: Open Folder in Container...](vscode-remote-container-open-folder.png)
- In the `Select Folder` dialog, create a new folder for the Python project, select it and click <kbd>Open</kbd>
- In the `Add Development Container Configuration Files` dialog, select `Show All Definitions...`
  ![Add Development Container Configuration Files](vscode-remote-container-show-all-definitions.png)
- Enter `p` and select `Python 3`
  ![Select Development Container Definition](vscode-remote-container-select-definition.png)
- Select a Python version
  ![Select Python version](vscode-remote-container-select-python-version.png)
- Select Python 3 dev container options. You can accept default selections. Click <kbd>Ok</kbd> to continue.
  ![Select Python 3 options](vscode-remote-container-select-python-options.png)
- The following message will be displayed in the `Remote Status` bar item during the setup of the dev container
  ![dev container: opening remote](vscode-remote-container-opening.png)
- When setup is finished, the following message will be displayed in the `Remote Status` bar item
  ![open dev container](vscode-remote-container-open.png)  
  In the `EXPLORER` pane you can find the folder and files that make up the dev container definition
  ![dev container definition](vscode-remote-container-created-files.png)
- Open a terminal with <kbd>Ctrl</kbd> + <kbd>\`</kbd> and enter `python --version` to verify that you can run python code in the container
  ![verify dev container setup](vscode-remote-container-verify.png)
- Create a Python module to debug/run it in Visual Studio Code

  ``` python
  def SayHello(name):
    message = "Hello dev container & " + name
    print(message)

  SayHello("docker")
  ```

  Set a breakpoint on line 3 by clicking left of line 3. A red dot will then appear.
  ![set breakpoint](vscode-remote-container-set-breakpoint.png)
- Press <kbd>F5</kbd> to start a debug session. In the figure below you can see the debugger stops at the breakpoint and in the variables panel, you can see the local variables and their value.
  ![debug session](vscode-remote-container-debug-session.png)
- When you are finished working in the Python project, click on the `Remote Status` bar to show the available commands for the open container in the Command palette. Select `Close Remote Connection`
  ![Close Remote Connection](vscode-remote-container-close.png)
