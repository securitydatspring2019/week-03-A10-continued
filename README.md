# Week-03 A10 continued

This week will be a continuation of week 1

## Environment Variables

One often used way to pass parameters to a service is through environment variable.
We will use environment variables for setting where the logs will located. And we will use the login scripts in the exercise below.

Basically the environment provides a medium through which the shell process can get or set settings and, in turn, pass these on to its child processes.

The environment is implemented as strings that represent key-value pairs. If multiple values are passed, they are typically separated by colon (:) characters. Each pair will generally look something like this:

``` bash
KEY=value1:value2:...
```
If the value contains significant white-space, quotations are used:

``` bash
KEY="value with spaces"
```

The keys in these scenarios are variables. They can be one of two types, environmental variables or shell variables.

**Environmental variables** are variables that are defined for the current shell and are inherited by any child shells or processes. Environmental variables are used to pass information into processes that are spawned from the shell.

We can see a list of all of our environmental variables by using the `env` or `printenv` commands. In their default state, they should function exactly the same:

``` bash
$ printenv
$ env
```

**Shell variables*** are variables that are contained exclusively within the shell in which they were set or defined. They are often used to keep track of ephemeral data, like the current working directory.

List shell variables with:

```bash
$ set
```

Create shell variables and use it:

```bash
$ TEST_VAR='Hello World!'
$ echo $TEST_VAR
```

To create an environment variable `export` a shell variable:

```bash
$ export TEST_VAR
```

or just

```bash
$ export OTHER_VAR="Another var"
```



### Common Environmental and Shell Variables

Some environmental and shell variables are very useful and are referenced fairly often.
Here are some common environmental variables that you will come across:

* **SHELL**: This describes the shell that will be interpreting any commands you type in. In most cases, this will be bash by default, but other values can be set if you prefer other options.
* **TERM**: This specifies the type of terminal to emulate when running the shell. Different hardware terminals can be emulated for different operating requirements. You usually won't need to worry about this though.
* **USER**: The current logged in user.
* **PWD**: The current working directory.
* **OLDPWD**: The previous working directory. This is kept by the shell in order to switch back to your previous directory by running cd -.
* **LS_COLORS**: This defines color codes that are used to optionally add colored output to the ls command. This is used to distinguish different file types and provide more info to the user at a glance.
* **MAIL**: The path to the current user's mailbox.
* **PATH**: A list of directories that the system will check when looking for commands. When a user types in a command, the system will check directories in this order for the executable.
* **LANG**: The current language and localization settings, including character encoding.
* **HOME**: The current user's home directory.
* **_**: The most recent previously executed command.

Full arcticle: [How To Read and Set Environmental and Shell Variables on a Linux VPS](https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps)

## Learning Goals

After this week you will be able to:
* Explain the difference between prevention, detection and recovery for systems you develop.
* Set up a firewall on ubuntu and examine the log files.
* Set up a remote logging server, and use that to register logins to an ubuntu server.
* **Plan how to use a cloud based logging service to enable anomaly detection.**

## Exercises

If your machine is hacked, you will not be able to look at the log files to see what happened. So it is good practice to log to a different machine. There are companies that offer this service, but we will try to set up a minimal system to do this ourselves to understand the principles.
There is a minimal logger system at:
https://github.com/securitydatspring2019/week-03-logger
You need to compile it, copy the jar file to a droplet, and run the jar file (you will be asked to install some Java).
In addition, the environment variable `LOG_PATH` must be set for the program to work.

To copy a file via ssh:
```bash
$ scp <file> <username>@<IP address or hostname>:<Destination>
```
Example:
```bash
$ scp logger.jar aka@200.199.198.197:logger.jar
```

We will next make a different droplet where we will see if anyone logs in. I call this system “HoneyPot”.
The idea is that we will make  a user on HoneyPot, with a username of `test`, and password: `123456` (see https://en.wikipedia.org/wiki/List_of_the_most_common_passwords).
The login script of that user should log that a user logged in.
* Can you find out which IP that user came from?
* Which other info do we have we could log?

To log it - there is the “curl” command on linux. Try to figure out what this command does:

```bash
$ curl -G  "http://loggingsystem:8888/logger" --data-urlencode "log=Someone knocked"
```

What does `curl` do in the first place
What is the `-G` option, and what is the strange option at the end
Can we omit any of the flags?


Micro cheatsheet for making a new user on ubunto for our purpose:

```bash
$ useradd -m james
$ passwd james
```

You have to allow ssh-connection via password:

Look in `/etc/ssh/sshd_config` for
```
# Global settings
…
PasswordAuthentication no
…
```
Change to `yes`, then tell the sshd service to reload its configuration:
```bash
$ service ssh reload
```
To terminate all processes you might have started under the user johndoe:

```bash
$ pkill -u johndoe
```

That stops all processes - also johndoe’s interactive bash
