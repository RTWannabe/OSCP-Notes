# Shells

## Upgrading to TTY shell \(Linux\)

First, run: `python -c "import pty; pty.spawn('/bin/bash')"`

Be sure to check for other versions of Python!

### Enable auto tab

Background the process using `Ctl + Z`

Enter `stty raw -echo`

Foreground the process using `fg`
