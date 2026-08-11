#This doc will record the the process of sending TCP segments in linux OS.
## tcp_sendmsg() in net/ipv4/tcp.c

### Role
Entry point for sending application data through TCP.

### Responsibility

1. Acquire the socket lock.
2. Call tcp_sendmsg_locked().

---

## tcp_sendmsg_locked()

### Role
Copy application data into the TCP send buffer.

### Responsibility

1. Validate the TCP connection state.
2. Find or allocate an appropriate skb in the write queue.
3. Copy application data into the skb.
4. Handle send buffer space and blocking/non-blocking behavior.
5. Push pending data when necessary through tcp_push().

Related RFC:
RFC9293

---

## tcp_push()
