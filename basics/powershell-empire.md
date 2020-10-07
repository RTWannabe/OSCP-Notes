# Powershell Empire

Empire is a PowerShell and python post-exploitation tool that is specifically designed for Active Directory environments.  Empire shares many features with MSF, but they differ in use.

#### Installation

```text
cd /opt
sudo git clone https://github.com/PowerShellEmpire/Empire.git
cd /Empire
sudo ./setup/install.sh
sudo ./empire
```

#### Listeners

```text
listeners - list active listeners
uselistener - list listener types
uselistener $type - selects listener type
info - provides details, displays required updates
set $paramater - set inputs parameters
execute - starts listener
back - returns to main listener
usestager - lists available stagers
```
