# micron_gcp

Using Visual Studio Code for Remote Development

Vertex AI Notebooks provide a very nice JupyterLab development environment for exploration of data sets, training models, etc.  Running an IDE in a web browser (like JupyterLab) is powerful, but can have some nuances to deal with.   Responsiveness of interaction for example, can slow down at times (pause/busy), disconnects can happen if you leave the browser session unattended for periods of time, etc.

Some modern IDE's for Windows and Linux desktop environments (PyCharm, VS Code, etc) provide the ability to develop against remotely running process or on a remote machine through SSH.   Some of these IDE's also support JupyterLab Notebooks (PyCharm, VS Code, etc) as well and can provide the same rendering as the browser version.  You may find this experience more familiar, more responsive and potentially less nuanced than a notebook session in your browser. 

This article is intended to help you setup a remote VS Code development connection to a Notebook instance using SSH connections from a Windows workstation/laptop. 

Pre-Requisites
Before you attempt this, you should:

Install Python (i.e. | chrome > SAM > anaconda)
Setup a GCP Vertex AI Notebook
Install a SSH client compatible with VS Code 
Git for Windows
Windows OpenSSH Client
Create SSH keys to enable Notebook remote SSH login
Install Visual Studio Code
Install the VS code Remote Development extension pack
Install the VS code Jupyter extentsion pack
