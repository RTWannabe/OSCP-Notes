# Tunneling/Port Forwarding

### SSH tunneling

Local port forwarding

`ssh <gateway> -L <local port to listen>:<remote host>:<remote port>`

Remote port forwarding

`ssh <gateway> -R <remote port to bind>:<local host>:<local port>`

Dynamic port forwarding

`ssh -D <local proxy port> -p <remote port> <target>`

HTTP tunneling – technique to encapsulate a protocol within HTTP

`HTTPTunnel or stunnel`

### Plink.exe

```text
plink.exe -ssh -l kali -pw ilak -R 10.11.0.4:1234:127.0.0.1:3306 10.11.0.4
```

```text
plink.exe -l root -pw mysecretpassword 192.168.0.101 -R 8080:127.0.0.1:8080
```

### Meterpreter

```text
portfwd add -l <attacker port> -p <victim port> -r <victim ip>
portfwd add -l 3306 -p 3306 -r 192.168.1.10
```


