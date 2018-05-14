# Hosting Jupyter Notebooks in Narvi

This tutorial explains the process of using Narvi instances for hosting Jupyter Notebooks. This is to have Narvi perform the computations, effectively freeing up user's own machine for other uses.

Pre-requisite is that you must have a valid account to Narvi. This means that you already have generated the necessary SSH-keypairs and have successfully been able to log in to the Narvi system.

This tutorial also omits all Python-related package configurations within Narvi. The expectation is that everything is ready and only the project-related files are missing. The only distinct configuration is the re-assignment of a certain environment variable, that can cause permission problems.

## Prepare PuTTY

Create first a profile called ``narvi`` with the following settings:

 - Session > Host Name: user@narvi.tut.fi
 - Session > Port: 22
 - Session > Connection Type: SSH
 - Connection > SSH > Auth: Locate the private key file for authentication

Create then another profile called ``narvi-nb``:

 - Session > Host Name: user@narvi.tut.fi
 - Session > Port: 22
 - Session > Connection Type: SSH
 - Connection > SSH > Auth: Locate the private key file for authentication
 - Connection > SSH > Tunnels: Add a port forwarding rule with the following settings
	-- Source port: 8888
	-- Destination: localhost:8888, Local

The first profile is for normal access to the Narvi, while the other also defines port forwarding rules for accessing the Jupyter Notebook server from the user's local machine via a browser.
	
## Copy the Project to Narvi

Log in to Narvi with the first configuration, then create an appropriate folder structure for the project:

	putty -load narvi
	mkdir project

Make sure that you have a folder in path ``~/project`` and then log out. Then you can copy your project files, such as databases and Python scripts to Narvi. To copy a whole project folder, use:

	pscp -r c:\project\* narvi:project/
	
Pay attention to slashes. Windows requires ``\``-slashes, where UNIX uses ``/``-slashes. Folders must end with a slash.	

## Jupyter Notebook server

Open up an SSH connection with the ``narvi-nb``:

	putty -load narvi-nb
	
The first thing is to get the Jupyter Notebook running in a computing node:

	screen
	source getNode.sh
	source activate env
	export XDG_RUNTIME_DIR=""
	jupyter notebook --no-browser --port=8888
	
Copy the address with the token, such as:

	http://localhost:8888/?token=b27f8f9a606aee521c4b91cbe8d75ccf04527a450127ba30
	
Take note of the computing node's name assigned to you, such as ``nag09``. This is visible at the start of the line after receiving the node. We then define port forwarding rules from the computing node to the Narvi frontend, which can then be accessed from the local computer. Press ``Ctrl-A-C`` to open another screen and run:

	ssh -L localhost:8888:localhost:8888 user@node
	
The ``user@node`` is in my case e.g. ``nevavuor@nag09``. You can then detach the screens by pressing ``Ctrl-A-D``. To open them again, run

	screen -r
	
in the Narvi front-end.
	
Lastly open a browser in your local computer and paste the copied address. Everything should work now.
