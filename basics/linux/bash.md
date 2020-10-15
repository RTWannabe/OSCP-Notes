# Bash

### Environment variables

* Global storage inherited by applications run during the session
* PATH - colon separated directory list
* Can view environment variables with `echo $variablename`
* You can define an environment variable using the `export` command
* `env` command will display all pre-defined PATH variables

### Tab completion

* Tab autocompletion enables quick command entry

### Bash history tricks

* Bash maintains a list of commands typed
* Can run commands from the history with !\#\#, where \#\# is line number
* $HISTSIZE and $HISTFILESIZE control .bash\_history

### Piping and redirection

* STDIN - standard input \(data fed into program\)
* STDOUT - standard output \(defaults to terminal\)
* STDERR - standard error \(defaults to terminal\)
* Redirecting
  * To a new file: `echo "test" > redirection.txt`
  * To an existing file: to append data `data >> redirection.txt`
  * From a file: `wc -m < redirection.txt`
* Piping
  * `cat error.txt | wc -m > count.txt`

### Text searching and manipulation


