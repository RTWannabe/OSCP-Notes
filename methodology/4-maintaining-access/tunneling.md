# Tunneling

### SSH tunneling

Local port forwarding

`ssh <gateway> -L <local port to listen>:<remote host>:<remote port>`

Remote port forwarding

`ssh <gateway> -R <remote port to bind>:<local host>:<local port>`

Dynamic port forwarding

`ssh -D <local proxy port> -p <remote port> <target>`

HTTP tunneling – technique to encapsulate a protocol within HTTP

`HTTPTunnel or stunnel`
