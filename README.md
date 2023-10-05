## HBNB - The Console

This repository contains the initial stage of a student project to build a clone of the AirBnB website. This stage implements a backend interface, or console, to manage program data. Console commands allow the user to create, update, and destroy objects, as well as manage file storage. Using a system of JSON serialization/deserialization, storage is persistent between sessions.

## How to use Fabric in Python
Author: PFB Staff Writer
Last Updated: August 27, 2020

What is Fabric?
Fabric is a Python library and command-line tool for streamlining the use of SSH
for application deployment or systems administration tasks. 

Typical usage involves creating a Python module containing one or more functions,
then executing them via the fab command-line tool.

You can execute shell commands over SSH, so you only need to have SSH running on
the remote machine. It interact with the remote machines that you specify as if
they were local. 
Installation
Fabric requires Python version 2.5 or 2.6 (Fabric has not yet been tested on
Python 3.x)

The most common ways of installing Fabric is via, pip, easy_install or via
the operating system's package manager:
pip install fabric

sudo easy_install fabric

sudo apt-get install fabric	# (the package is typically called fabric or python-fabric.)
Please read the official documentation for more information (dependancies etc..) 
Fabric Usage
On their website they write:

"it provides a basic suite of operations for executing local or remote shell
commands (normally or via sudo) and uploading/downloading files, as well as
auxiliary functionality such as prompting the running user for input, or
aborting execution"

Having that information, let's move on.
The installation process added a Python script called fab to a directory in
your path. 

This is the script which will be used to make everything happen with Fabric. 

Just running fab from the command-line won't do much at all.

In order to do anything interesting, we'll need to create our first fabfile. 

Before we do that I would like to show some of the functions in Fabric. 
Fabric functions
Fabric provides a set of commands in fabric.api that are simple but powerful.

With Fabric, you can use simple Fabric calls like
local	# execute a local command)
run	# execute a remote command on all specific hosts, user-level permissions)
sudo	# sudo a command on the remote server)
put	# copy over a local file to a remote destination)
get	# download a file from the remote server)
prompt	# prompt user with text and return the input (like raw_input))
reboot	# reboot the remote system, disconnect, and wait for wait seconds)
Authentication
Fabric relies on the SSH Model, you can use SSH Keys but you can also control
access to root via sudoers. 

The user on the server does not need to be added to "~/.ssh/authorized_keys",
but if it is you don't have to type the password every time you want to execute
a command.

If you ever have to disable the access to a user, just turn off their SSH account.
The Fabfile
You can load Python modules with Fabric. 

By default, it looks for something named either fabfile or fabfile.py. 

This is the file that Fabric uses to execute tasks. 

Each task is a simple function.

The fabfile should be in the same directory where you run the Fabric tool. 

The fabfile is where all of your functions, roles, configurations, etc. will
be defined. 

It's just a little bit of Python which tells Fabric exactly what it needs to do. 

The "fabfile.py" file only has to be stored on your client. 

An SSH server like OpenSSH needs to be installed on your server and an
SSH client needs to be installed on your client.
Creating a Fabfile
To start just create a blank file called fabfile.py in the directory you’d like
to use the fabric commands from.

You basically write rules that do something and then you (can) specify on which
servers the rules will run on. 

Fabric then logs into one or more servers in turn and executes the shell commands 
defined in "fabfile.py". 

If you are located in the same dir as "fabfile.py" you can go "fab --list"
to see a list of available commands and then "fab [COMMAND_NAME]" to execute a
command.

From https://github.com/fabric/fabric

Below is a small but complete "fabfile" containing a single task:
from fabric.api import run
def host_type():
    run('uname -s')
Once a task is defined, it may be run on one or more servers, like so
$ fab -H localhost,linuxbox host_type

[localhost] run: uname -s
[localhost] out: Darwin
[linuxbox] run: uname -s
[linuxbox] out: Linux

Done.
Disconnecting from localhost... done.
Disconnecting from linuxbox... done.
You can run fab -h for a full list of command line options

In addition to use via the fab tool, Fabric's components may be imported into
other Python code, providing a Pythonic interface to the SSH protocol suite at
a higher level than that provided by e.g. the ssh library, 
(which Fabric itself uses.)
Connecting to remote servers
As you can see above, Fabric will look for the function host_type in the
fabfile.py of the current working directory.
In the next example we can see how the Fabric Api uses an internal env variable
to manage information for connecting to remote servers.

The user is the user name used to login remotely to the servers and the hosts is
a list of hosts to connect to. 

Fabric will use these values to connect remotely for use with the run
and sudo commands. 

It will prompt you for any passwords needed to execute commands or connect to
machines as this user. 
# First we import the Fabric api
from fabric.api import *

# We can then specify host(s) and run the same commands across those systems
env.user = 'username'

env.hosts = ['serverX']

def uptime():
    run("uptime")
This will first load the file ~/fabfile.py

Compiling the file into ~/fabfile.pyc # a standard pythonism

Connect via SSH to the host 'serverX'

Using the username 'username'

Show the output of running the command "uptime" upon that remote host.

Now, when we run the fabfile, we can see that we can connect to the remote server.
$ fab uptime
If you have different usernames on different hosts, you can use:
env.user = 'username'
env.hosts = ['userX@192.168.1.1', 'serverX']
Now userX username would be used on 192.168.1.1 and 'username' would be used
on 'serverX'
Roles
You can define roles in Fabric, and only run actions on servers tied to a
specific role. 

This script will run get_version on hosts members of the role "webservers",
by running first on www1, then www2 etc.
fab -R webservers
from fabric.api import *

# Define sets of servers as roles

env.roledefs = {
    'webservers': ['www1', 'www2', 'www3', 'www4', 'www5'],
    'databases': ['db1', 'db2']
}

# Set the user to use for ssh
env.user = 'fabuser'

# Restrict the function to the 'webservers' role

@roles('webservers')

def get_version():
    run('cat /etc/issue')
# To run get_version on both roles (webservers and databases);
$ fab get_version
@roles ('webservers', 'databases')

    def get_version():

 run('cat /etc/issue')
Any function you write in your fab script can be assigned to one or more roles. 

You can also include a single server in multiple roles.
Copy a directory to a remote machine
I would like to end this introduction of Fabric, by showing an example of
how to copy a directory to a remote machine.
from fabric.api import *

env.hosts = ['userX@serverX']

def copy():
    # make sure the directory is there!
    run('mkdir -p /home/userX/mynewfolder')

    # our local 'localdirectory' (it may contain files or subdirectories)
    put('localdirectory', '/home/userX/mynewfolder')
Conclusion
