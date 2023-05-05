# Sshell

A simple UNIX command interpreter written as part of the low-level programming at Holberton School.

## Description

Sshell is a simple UNIX command language interpreter that reads commands from either a file or standard input and executes them.

## Invocation

Usage: Sshell [filename]

To invoke Sshell, compile all .c files in the repository and run the resulting executable:
```
gcc *.c -o sshell
./sshell
```
Sshell can be invoked both interactively and non-interactively. If shellby is invoked with standard input not connected to a terminal, it reads and executes received commands in order.

Example:
```
$ echo "echo 'hello'" | ./sshell
'hello'
$
```
If sshell is invoked with standard input connected to a terminal, an interactive shell is opened. When executing interactively, sshell displays the prompt $  when it is ready to read a command.

Example:
```
$./sshell
$
```
Alternatively, if command line arguments are supplied upon invocation, sshell treats the first argument as a file from which to read commands. The supplied file should contain one command per line. Sshell runs each of the commands contained in the file in order before exiting.

Example:
```
$ cat test
echo 'hello'
$ ./sshell test
'hello'
$
```
## Environment
Upon invocation, sshell receives and copies the environment of the parent process in which it was executed. This environment is an array of name-value strings describing variables in the format NAME=VALUE. A few key environmental variables are:
### HOME
The home directory of the current user and the default directory argument for the cd builtin command.
```
$ echo "echo $HOME" | ./sshell
/home
```
### PWD
The current working directory as set by the cd command.
```
$ echo "echo $PWD" | ./sshell
/home/holbertonschool-simple_shell
```
### OLDPWD
The previous working directory as set by the cd command.
```
$ echo "echo $OLDPWD" | ./sshell
/home
```
### PATH
A colon-separated list of directories in which the shell looks for commands. A null directory name in the path (represented by any of two adjacent colons, an initial colon, or a trailing colon) indicates the current directory.

### Command Execution
After receiving a command, sshell tokenizes it into words using " " as a delimiter. The first word is considered the command and all remaining words are considered arguments to that command. Sshell then proceeds with the following actions:

1. If the first character of the command is neither a slash (\) nor dot (.), the shell searches for it in the list of shell builtins. If there exists a builtin by that name, the builtin is invoked.
2. If the first character of the command is none of a slash (\), dot (.), nor builtin, sshell searches each element of the PATH environmental variable for a directory containing an executable file by that name.
3. If the first character of the command is a slash (\) or dot (.) or either of the above searches was successful, the shell executes the named program with any remaining given arguments in a separate execution environment.
## Exit Status
Sshell returns the exit status of the last command executed, with zero indicating success and non-zero indicating failure.

If a command is not found, the return status is 127; if a command is found but is not executable, the return status is 126.

All builtins return zero on success and one or two on incorrect usage (indicated by a corresponding error message).

## Signals
While running in interactive mode, sshell ignores the keyboard input Ctrl+c. Alternatively, an input of end-of-file (Ctrl+d) will exit the program.
```
$ ./sshell
$ ^C
$ ^C
$
```
User hits Ctrl+d in the third line.
## Variable Replacement
Sshell interprets the $ character for variable replacement.

#### $ENV_VARIABLE
ENV_VARIABLE is substituted with its value.

$?
? is substitued with the return value of the last program executed.
Example:
```
$ echo "echo $?" | ./sshell
0
```
##### $$
The second $ is substitued with the current process ID.
Example:

```
$ echo "echo $$" | ./sshell
6494
```
### Comments
Sshell ignores all words and characters preceeded by a # character on a line.
Example:
```
$ echo "echo 'hello' #this will be ignored!" | ./sshell
'hello'
```
### Operators
Sshell specially interprets the following operator characters:
###### ; - Command separator
Commands separated by a ; are executed sequentially.
Example:
```
$ echo "echo 'hello' ; echo 'world'" | ./sshell
'hello'
'world'
```
##### && - AND logical operator
command1 && command2: command2 is executed if, and only if, command1 returns an exit status of zero.

Example:
```
$ echo "error! && echo 'hello'" | ./sshell
./sshell: 1: error!: not found
$ echo "echo 'all good' && echo 'hello'" | ./sshell
'all good'
'hello'
```
#### || - OR logical operator
command1 || command2: command2 is executed if, and only if, command1 returns a non-zero exit status.

Example:
```
$ echo "error! || echo 'but still runs'" | ./sshell
./sshell: 1: error!: not found
'but still runs'
```
The operators && and || have equal precedence, followed by ;.
### Sshell Builtin Commands 
###### cd
Usage: cd [DIRECTORY]
Changes the current directory of the process to DIRECTORY.
If no argument is given, the command is interpreted as cd $HOME.
If the argument - is given, the command is interpreted as cd $OLDPWD and the pathname of the new working directory is printed to standad output.
If the argument, -- is given, the command is interpreted as cd $OLDPWD but the pathname of the new working directory is not printed.
The environment variables PWD and OLDPWD are updated after a change of directory.
###### alias
Usage: alias [NAME[='VALUE'] ...]
Handles aliases.
alias: Prints a list of all aliases, one per line, in the form NAME='VALUE'.
alias NAME [NAME2 ...]: Prints the aliases NAME, NAME2, etc. one per line, in the form NAME='VALUE'.
alias NAME='VALUE' [...]: Defines an alias for each NAME whose VALUE is given. If name is already an alias, its value is replaced with VALUE.
Example:
```
$ ./sshell
$ alias show=ls
$ show
AUTHORS            builtins_help_2.c  errors.c         linkedlist.c        shell.h       test
README.md          env_builtins.c     getline.c        locate.c            sshell
alias_builtins.c   environ.c          helper.c         main.c              split.c
builtin.c          err_msgs1.c        helpers_2.c      man_1_simple_shell  str_funcs1.c
builtins_help_1.c  err_msgs2.c        input_helpers.c  proc_file_comm.c    str_funcs2.c
```
###### exit
Usage: exit [STATUS]
Exits the shell.
The STATUS argument is the integer used to exit the shell.
If no argument is given, the command is interpreted as exit 0.
Example:
```
$ ./sshell
$ exit
```
###### env
Usage: env
Prints the current environment.
Example:
```
$ ./sshell
$ env
NVM_DIR=/home/.nvm
...
```
###### setenv
Usage: setenv [VARIABLE] [VALUE]
Initializes a new environment variable, or modifies an existing one.
Upon failure, prints a message to stderr.
Example:
```
$ ./sshell
$ setenv NAME Jassem
$ echo $NAME
Jassem
```
###### unsetenv
Usage: unsetenv [VARIABLE]
Removes an environmental variable.
Upon failure, prints a message to stderr.
Example:
```
$ ./sshell
$ setenv NAME Jassem
$ unsetenv NAME
$ echo $NAME

$
```
#### Author
* Firas Msekni
#### Acknowledgements
Sshell emulates basic functionality of the sh shell.
This project was written as part of the curriculum for Holberton School. Holberton School is a campus-based full-stack software engineering program that prepares students for careers in the tech industry using project-based peer learning.
