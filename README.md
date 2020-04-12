# Bash snippets
Because I keep forgetting

## Index

<!-- toc -->

- [Bash Shebang && starting point](#bash-shebang--starting-point)
- [Checks](#checks)
  * [If a directory (doesn't) exist](#if-a-directory-doesnt-exist)
  * [If a file exists](#if-a-file-exists)
  * [If a command exists](#if-a-command-exists)
- [Language](#language)
  * [functions](#functions)
- [General unix things](#general-unix-things)
  * [redirections of stdout and stderr](#redirections-of-stdout-and-stderr)
  * [Run command in the background](#run-command-in-the-background)
  * [Chaining commands](#chaining-commands)

<!-- tocstop -->

## Bash Shebang && starting point

```bash
#!/usr/bin/env bash
set -o errexit  # Exit on most errors (see the manual)
set -o errtrace # Make sure any error trap is inherited
set -o nounset  # Disallow expansion of unset variables
set -o pipefail # Use last non-zero exit code in a pipeline
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



