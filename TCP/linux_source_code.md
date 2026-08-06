# This doc will record the TCP interaction in linux OS.
# Receive incoming segments
<img width="364" height="249" alt="image" src="https://github.com/user-attachments/assets/167ea61d-f828-435d-9802-6ff995a67b25" />

## `tcp_v4_rcv()`
### Role: TCP IPv4 receive entry.  
### Responsibility  
1. Validate the incoming TCP packet, such as header len, checksum, packet format.  
2. find their socket and check socket.  
3. dispatch packets according to socket state.(SYN_AWAIT/ESTABLISHMENT/...).  
4. handle child socket creation for pending connections.
    - TCP_NEW_SYN_RECV
    - TCP_TIME_WAIT  
5. Forward packets to tcp_v4_do_rcv().  

Related RFC: RFC9293

## `tcp_v4_do_rcv()`
### Role: Dispatch TCP packets according to current TCP state after socket lookup.
### Responsibility  
1. LISTEN
    → Process connection establishment(tcp_child_process).
2. ESTABLISHED
    → Fast path (tcp_rcv_established).
3. Other states
    → TCP state machine (tcp_rcv_state_process).  

Related RFC: RFC9293

## `tcp_child_process()`
### Role: Wrapper of child socket processing.
### Responsibility  
1. Record NAPI id for packet ownership and polling context.
2. Child sock start connection(tcp_rcv_state_process).

Related RFC: RFC9293

## `tcp_rcv_state_process()`
### Role: the core state controller  
LISTEN

↓

SYN_RECV

↓

ESTABLISHED

↓

FIN_WAIT1

↓

FIN_WAIT2

↓

LAST_ACK

↓

TIME_WAIT

### Responsibility  
Filter the packet in three way handshake which does not have ack
1. LISTEN
    → Start connection and consume socket buffer
2. SYN_SENT
    → Continue to three way handshake(tcp_rcv_synsent_state_process).
check the ack
3. SYN_RECV
    → Validate the packet header info then change into ESTABLISHED
4. FIN_WAIT1
    → Set state into FIN_WAIT2
6. CLOSING
    → Set state into TIME_WAIT and wait 2MSL
7. LAST_ACK
    → Check ack then close the socket
Process data then check state again

Related RFC: RFC9293

## `tcp_rcv_established()`
### Role: Fast receive path for TCP_ESTABLISHED connections.
### Responsibility  
1. Fast-path validation
2. Process ACK
3. Queue payload
4. Handle urgent/out-of-order data

Related RFC: RFC9293

# Process the acknowledgment information carried by incoming TCP segments.
## `tcp_ack()`
### Role: process the ack in segments
### Responsibility
1. Validate ACK information by adding flag.
2. Advance snd_una and remove acknowledged packets from the retransmission queue -> tcp_clean_rtx_queue() Update RTT estimation ->  tcp_ack_update_rtt()
3. Detect duplicate ACKs -> tcp_process_tlp_ack()
4. Trigger congestion-control and fast retransmission when required -> tcp_cong_control() && tcp_fastretrans_alert()

Related RFC: 
RFC9293
- ACK validation
- snd_una update
- Retransmission queue
- RTT measurement

RFC5681
- Duplicate ACK
- Fast Retransmit
- Congestion Control
