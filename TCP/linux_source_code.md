# This doc will record the TCP interaction in linux OS.

## `tcp_v4_rcv()`
Role: TCP IPv4 receive entry.  
1.Validate the incoming TCP packet, such as header len, checksum, packet format.  
2.find their socket and check socket.  
3.dispatch packets according to socket state.(SYN_AWAIT/ESTABLISHMENT/...).  
4.handle child socket creation for pending connections.
    - TCP_NEW_SYN_RECV
    - TCP_TIME_WAIT  
5. Forward packets to tcp_v4_do_rcv().  
Related RFC: RFC9293

## `tcp_v4_do_rcv()`
Role: Dispatch packets after socket lookup.
1. LISTEN
    → Process connection establishment.
2. ESTABLISHED
    → Fast path (tcp_rcv_established).
3. Other states
    → TCP state machine (tcp_rcv_state_process).  
Related RFC: RFC9293
