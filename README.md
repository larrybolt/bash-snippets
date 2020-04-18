# Bash snippets
Because I keep forgetting

## Index

<!-- toc -->

- [Bash Shebang && starting point](#bash-shebang--starting-point)
- [Input, prompt and parameters/arguments](#input-prompt-and-parametersarguments)
  * [Check if argument is set](#check-if-argument-is-set)
  * [Have defaults for arguments](#have-defaults-for-arguments)
  * [Prompt for user confirmation](#prompt-for-user-confirmation)
- [Checks](#checks)
  * [If a directory (doesn't) exist](#if-a-directory-doesnt-exist)
  * [If a file exists](#if-a-file-exists)
  * [If a command exists](#if-a-command-exists)
- [Language](#language)
  * [functions](#functions)
  * [Equality Operators](#equality-operators)
- [General unix things](#general-unix-things)
  * [redirections of stdout and stderr](#redirections-of-stdout-and-stderr)
  * [Run command in the background](#run-command-in-the-background)
  * [Chaining commands](#chaining-commands)
- [Specific unixy things](#specific-unixy-things)
  * [Grep to get ip address from stream](#grep-to-get-ip-address-from-stream)
  * [Group and count groups](#group-and-count-groups)
  * [Split by character and get nth item](#split-by-character-and-get-nth-item)
  * [Get the Nth first or last lines](#get-the-nth-first-or-last-lines)
  * [Get the Nth lines before or after a match](#get-the-nth-lines-before-or-after-a-match)
- [Exiting](#exiting)

<!-- tocstop -->

## Bash Shebang && starting point

```bash
#!/usr/bin/env bash
set -o errexit  # Exit on most errors (see the manual)
set -o errtrace # Make sure any error trap is inherited
set -o nounset  # Disallow expansion of unset variables
set -o pipefail # Use last non-zero exit code in a pipeline
```

## Input, prompt and parameters/arguments

When running a script the script/command is stored in `$0`, the arguments are passed to `$1`, `$2` ... etc.

### Check if argument is set

```bash
# command requires two arguments
if [[ $# -ne 2  ]]; then
  echo "usage: $0 ARGS1 ARG2"
  exit 1
fi
```

### Have defaults for arguments

```bash
ARG1=${1:-DEFAULTVALUE}
```

### Prompt for user confirmation

```bash
read -r -p "Are you ready for command1? [y/N] " response
if [[ $response =~ ^([yY][eE][sS]|[yY])$ ]]; then
  command1
fi

# exit unless the user enters yes:
read -r -p "Continue? [y/N] " response
if [[ ! $response =~ ^([yY][eE][sS]|[yY])$ ]]; then
  exit
fi
```

## Checks

### If a directory (doesn't) exist
```bash
if test -d "filename"; then
  echo "filename does exist"
fi

if test ! -d "node_modules"; then
  echo "node_modules doesn't exist, installing!"
  npm install
fi
```

### If a file exists
```bash
if test -f "./docker-compose.yml"; then
	echo "docker-compose.yml file found, redeploying"
	docker-compose up --build -d
fi
```

### If a command exists
```bash
if ! [ -x "$(command -v ag)" ]; then
  echo 'Error: ag is not installed.' >&2
  brew install ag
  exit 1
fi
```

## Language
### functions
```bash
hello_world () {
   echo "hello world, $1!"
}

hello_world human
```

### Equality Operators

```bash
# strings
a=foo
[ "$a" = foo ]; echo "$?"  # POSIX, use this, works in bash, sh, zsh...
[ "$a" == foo ]; echo "$?" # only works in bash

# numbers
b=123
[ "$b" -eq 123 ]; echo "$?" # equals
[ "$b" -ne 432 ]; echo "$?" # not equals

[ "$b" -lt 200 ]; echo "$?" # <  lesser than
[ "$b" -gt 100 ]; echo "$?" # >  greater than
[ "$b" -le 123 ]; echo "$?" # <= lesser or equal
[ "$b" -ge 123 ]; echo "$?" # >= greater or equal
```

## General unix things

### redirections of stdout and stderr
```bash
# to separate files
command > filename_for_stdout 2> filename_for_stderr
# eg:
command > $HOME/command.log 2> $HOME/command.error.log

# to same file
command &> filename

# ignore stdout and stderr
command &> /dev/null
```
### Run command in the background
```bash
# will exit when you logout of the shell or close the terminal
command &
# eg:
sleep 10 && date >> testing.log &

# will keep running if you logout / close the shell
nohup command &
# eg:
nohup sleep 10 && date >> testing.log &
```

### Chaining commands

```bash
# run only if each of them is successful
command1 && command2
# eg:
mkdir -p this/path/will-exist && mv file this/path/will-exist/

# run next one regardless of previous one
command1; command2
# eg:
npm install; tput bel

# run next one only if previous one returned an error
command1 || command2
# eg:
rm directory || echo "Directory doesn't exist!"

```

## Specific unixy things

### Grep to get ip address from stream

```bash
cat filename | grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b"
```

### Group and count groups

```bash
cat filename | sort | uniq -c

# eg, get ip address from running docker containers, sorted from most often to least
docker-compose logs | grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" | sort | uniq -c | sort -r
```

### Split by character and get nth item

```bash
# split by : and get Nth item
echo "col1:col2:col3:col4" | cut -d: -f1   # col1
echo "col1:col2:col3:col4" | cut -d: -f2   # col2
echo "col1:col2:col3:col4" | cut -d: -f2-3 # col2:col3
echo "col1:col2:col3:col4" | cut -d: -f2-  # col2:col3:col4

# get last field
echo "col1:col2:col3:col4" | rev | cut -d: -f1 | rev # col4

# split by space and get Nth item
echo "col1 col2 col3 col4" | cut -d' ' -f2
```

### Get the Nth first or last lines

```bash
# Get the 10 first lines of filename
cat filename | head -10

# Get the 10 last lines of filename
cat filename | tail -10
```

### Get the Nth lines before or after a match

```bash
cat filename | grep -A 10 -o 'amis.*'
```



## Exiting

```bash
# all is well
exit

# exiting because of an error
exit 1
```



