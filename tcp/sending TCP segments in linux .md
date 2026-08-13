#This doc will record the the process of sending TCP segments in linux OS.
Application

 │
 
 ▼
 
tcp_sendmsg()

 │
 
 ▼
 
tcp_sendmsg_locked()

 │
 
 ▼
 
Write Queue

 │
 
 ▼
 
tcp_push()

 │
 
 ▼
 
__tcp_push_pending_frames()

 │
 
 ▼
 
tcp_write_xmit()

 │
 
 ▼
 
tcp_transmit_skb()

 │
 
 ▼
 
ip_queue_xmit()

 │
 
 ▼
 
IP level

 │
 
 ▼
 
NIC


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

### Role

Decide whether pending TCP data should be pushed for transmission.

### Responsibility

1. Check the tail skb and determine whether the pending data should be pushed.
2. Consider application hints such as MSG_MORE and TCP_NODELAY when deciding whether to push.
3. Call __tcp_push_pending_frames() to continue the TCP transmission path.

---

## __tcp_push_pending_frames()

### Role

check connection state and push sk

### Responsibility

1. Check state connection is not closed
2. Call tcp_write_xmit() to push date

---

## tcp_write_xmit()

### Role

Decide which TCP segments in the write queue are allowed to be transmitted.

### Responsibility

1. Update TCP transmission timestamp.
2. Check the write queue and determine how much data can be transmitted.
3. Consider transmission constraints such as:
   - congestion window (cwnd)
   - receive window (rwnd)
   - Nagle / nonagle state
   - TCP recovery state
   - pacing
4. Determine the appropriate segment size, including TSO/GSO considerations.
5. Transmit eligible skb by calling tcp_transmit_skb().
6. Update transmission-related state and statistics.
7. Continue transmitting while the sending conditions allow.
8. Return the transmission result/status.

---

## tcp_transmit_skb()

### Role

Prepare and transmit a TCP skb.

### Responsibility

1. Prepare the skb for transmission.
2. Call __tcp_transmit_skb() to construct and transmit the TCP segment.

---

## __tcp_transmit_skb()

### Role

Construct a TCP segment and pass it to the IP layer.

### Responsibility

1. Prepare the TCP header.
2. Set TCP header fields such as:
   - source/destination port
   - sequence number
   - acknowledgment number
   - flags
   - window
3. Handle TCP options when required.
4. Calculate/set the TCP checksum.
5. Update transmission-related TCP state.
6. Pass the skb to the IP layer through ip_queue_xmit().
