#This doc will record the the process of sending TCP segments in linux OS.
## tcp_sendmsg() in net/ipv4/tcp.c
### Role: send tcp segments
### Responsibility
lock the sock and call tcp_sendmsg_locked()

## tcp_sendmsg_locked()
### Role send tcp segments with a locked sock
