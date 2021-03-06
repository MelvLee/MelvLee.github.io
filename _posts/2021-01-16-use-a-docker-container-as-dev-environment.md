---
layout: post
title: Use a Docker container as a full-featured development environment
css: ['/assets/css/style.css']
tags: [Dev Containers, Remote - Containers, Visual Studio Code]
---
Have you ever wanted to try a new programming language but gave up because of all the prerequisites you need to install? Or did you want to make a small change in a (old) project only to find out that you need another version of the programming language than the one currently installed on your PC? With **development containers** (or **dev containers**), this can be something of the past.  
According to the [documentation][1], a dev container is a Docker container, setup to run an application or to sandbox tools, libraries, and/or runtimes needed for working with a codebase. The Visual Studio Code **Remote - Containers** extension makes it easy to setup a dev container and use it as a full-featured development environment.
Once setup, the configuration files (container definition) can be stored with the other project files in a version control system, so that whenever needed the same development environment can be re-created using the container definition.

## Prerequisites

The following software must be installed to use dev containers:

- [Docker Desktop][2]
- [Visual Studio Code][3]
- [Remote - Container][4] VS Code extension

## Setup a dev container for a new project

To setup a Python 3 dev container:

- Start Visual Studio Code
- Open the **Command Palette** with <kbd>F1</kbd> or <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd>
- In the **Command Palette**, enter `remote container` and select `Remote Containers: Open Folder in Container...`

  ![Remote Containers: Open Folder in Container...](/img/vscode-remote-container-open-folder.png)
- In the **Select Folder** dialog, create a new folder for the Python project, select it and click <kbd>Open</kbd>
- In the **Add Development Container Configuration Files** dialog, select `Show All Definitions...`

  ![Add Development Container Configuration Files](/img/vscode-remote-container-show-all-definitions.png)
- Enter `p` and select `Python 3`

  ![Select Development Container Definition](/img/vscode-remote-container-select-definition.png)
- Select a `Python version`

  ![Select Python version](/img/vscode-remote-container-select-python-version.png)
- Select/Unselect `Python 3 dev container options`. Click <kbd>Ok</kbd> to continue.

  ![Select Python 3 options](/img/vscode-remote-container-select-python-options.png)
- The following message will be displayed in the **Remote Status** bar item during the setup of the dev container

  ![dev container: opening remote](/img/vscode-remote-container-opening.png)
- When the dev container setup is finished, the following message will be displayed in the **Remote Status** bar item

  ![open dev container](/img/vscode-remote-container-open.png)  
  In the `EXPLORER` pane, there is a **.devcontainer** folder. The files in this folder contains the dev container definition

  ![dev container definition](/img/vscode-remote-container-created-files.png)
- Open a terminal with <kbd>Ctrl</kbd> + <kbd>\`</kbd> and enter `python --version` to verify that you can run python code in the container

  ![verify dev container setup](/img/vscode-remote-container-verify.png)
- Create a Python module to test the debug and run functionality of Visual Studio Code

  ``` python
  def SayHello(name):
    message = "Hello dev container & " + name
    print(message)

  SayHello("docker")
  ```

  Set a breakpoint on line 3 by clicking left of line 3. A red dot will then appear.

  ![set breakpoint](/img/vscode-remote-container-set-breakpoint.png)
- Press <kbd>F5</kbd> and select `Python File` from the **Select a debug configuration** options to start a debug session.

  ![select a debug configuration](/img/vscode-remote-container-select-debug-configuration.png)

  The debugger executes the module and stops at the breakpoint. In the variables panel, you can see the local variables and their value.

  ![debug session](/img/vscode-remote-container-debug-session.png)
- To stop and exit the dev container, click on the **Remote Status** bar item and select the `Close Remote Connection` option from the **Command palette**.

  ![Close Remote Connection](/img/vscode-remote-container-close.png)

## Conclusion

In this article, you have learned how to setup a dev container. There are a large number of predefined container definitions to select from. With dev containers, you can now setup isolated environments to experiment without the fear of polluting your PC.

[1]: https://code.visualstudio.com/docs/remote/containers
[2]: https://www.docker.com/products/docker-desktop
[3]: https://code.visualstudio.com
[4]: https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers
