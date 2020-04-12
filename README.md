# Bash snippets
Because I keep forgetting

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
command > $HOME/command.log 2> $HOME/command.error.log

# to same file
command &> ./command.log

# ignore stdout and stderr
command &> /dev/null
```
### Run command in the background
```bash
# will exit when you logout of the shell or close the terminal
nohup sleep 10 && date >> testing.log &

# will keep running if you logout / close the shell
nohup sleep 10 && date >> testing.log &
```

