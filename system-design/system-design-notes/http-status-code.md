# System, Node, Network

## System Behaviors

以下為System在不同狀況下，有可能產生的Behaviors。我們分成三部分討論：  
\(1\) Network Behaviors  
\(2\) Nodes Behaviors  
\(3\) Timing Behaviors

### 1. Network Behavior

Assume bidirectional point-to-point communication between nodes with one of the following network behavior:

* **Reliable** \(perfect\) links Message is received, message may be reordered.             &lt; ---- \|
* **Fair-Loss** links                                                                                  \| retry + de-duplicate Messages may be lost, duplicated, or reordered.                ---- \| If you keep retrying, a message eventually gets through. &lt;---\|cryptographic protocol
* **Arbitrary** links                                                                                 \|  **TLS \(Transport** A malicious adversary may interfere with messages.        -----\|     **Layer Security\)** \(eavesdrop, modify, drop, spoof, replay\)

Network Partition: some links dropping/delaying all messages for extended period of time.

### 2. Node Behavior

A node that is not faulty is called '`correct node`', otherwise the node could be faulty. Each node executes a specified algorithm, with the following node behavior: 

* **Crash-Stop** \(fail-stop\) A node is faulty if it crashes, and never gets recovered.
* **Crash-Recovery** \(fail-recovery\) A node may crash at any moment, losing its in-memory state. But it may resume executing sometime later.
* **Byzantine** \(fail-arbitrary\) A node is faulty if it behaves abnormal/deviates from the algorithm. Such as crashing, malicious behavior.

### 3. Timing/Synchrony Behavior

Between network and nodes, timing part will have the following behavior:  

* **Synchronous** Message latency is not greater than a known upper bound. Nodes execute algorithm at a known speed.
* **Partially Synchronous** Sometime async, sometime sync.
* **Asynchronous** Messages can be delayed arbitrarily. Nodes can pause execution arbitrarily.  No timing guarantees at all.

**What kind of situations could increase asynchrony/latency? sync -&gt; async**  
In network:  
\(1\) Message loss requiring **retry**  
\(2\) **Congestion/contention** causing queuing  
\(3\) **Network/route reconfiguration**   
  
In nodes:  
\(1\) Operating system **scheduling issue**   \(e.g. priority inversion\)  
\(2\) Stop-the-world **garbage collection pauses**  
\(3\) Page faults, swap, thrashing

<table>
  <thead>
    <tr>
      <th style="text-align:left"></th>
      <th style="text-align:left">Summary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p><b>Network Behavior</b>
        </p>
        <ul>
          <li>Reliable</li>
          <li>Fair-Loss</li>
          <li>Arbitrary</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p><b>Nodes Behavior</b>
        </p>
        <ul>
          <li>Crash-Stop</li>
          <li>Crash-Recovery</li>
          <li>Byzantine</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p><b>Timing Behavior</b>
        </p>
        <ul>
          <li>Synchronous</li>
          <li>Partially Synchronous</li>
          <li>Asynchronous</li>
        </ul>
        <p>What kind of issues could increase latency?</p>
        <p>In network
          <br />(1) message loss, need retry</p>
        <p>(2) network congestion</p>
        <p>(3) network reconfig</p>
        <p></p>
        <p>In nodes</p>
        <p>(1) scheduling/priority issue
          <br />(2) garbage collection caused pause
          <br />(3) page faults...</p>
      </td>
    </tr>
  </tbody>
</table>

## 2-1. Latency and Bandwidth

### Latency

**What is Latency \(延遲性\)?**  
Latency is 1\) **time until a message arrives**.   
                   2\) the time it takes to communicate from one device to another.

* In the same building/data center: ~ 1ms
* One continent to another: ~ 100ms
* Hard drives in a van: ~ 1 day

### Bandwith

**What is Bandwith \(可乘載的量\)?**  
Bandwith is **data volume per unit time.**

* 3G cellular data: ~ 1 Mb/s
* Home broadband: ~ 10 Mb/s
* Hard drives in a van:  50 TB/box ~ 1Gb/s 

## 2-2. HTTP and TCP

**How does TCP send data via the Internet?**  
TCP sends network packets \(each packet is a small size.\)   
HTTP uses TCP underneath. \(Request\) TCP breaks down these big messages, into small network packets that are small enough that the network can deliver them. And then on the recipient side \(Response\), TCP puts all of the network packets back again to give us one large chunk of bytes.

## 2-3. RPC - Remote Procedure Call  

Ideally, RPC makes a call to a remote function look the same as a local function call. 

### RPC in Enterprise Systems

**Service Oriented Architecture \(SOA\) / "microservices"**  
SOA meaning splitting a large software application into multiple services \(on multiple nodes\) that communicate via RPC. 



## 2-9. HTTP

### HTTP Status Code

1. Informational responses \(`100`–`199`\)
2. Successful responses \(`200`–`299`\)
3. Redirects \(`300`–`399`\)
4. Client errors \(`400`–`499`\)
5. Server errors \(`500`–`599`\)

{% embed url="https://developer.mozilla.org/en-US/docs/Web/HTTP/Status" %}

## 3. Replication

What does replication means?  
\# Keeping a copy of the same data on multiple nodes \(e.g. DBs, filesystems, caches\)  
\# A node that has a copy of the data is called a **replica**.  
\# If some replicas are faulty, others are still accessible  
\# Replication spread load across many replicas

> Note: Learn RAID \(Redundant Array of Independent Disks\)

### Retrying State Updates

### 3-2. Idempotence \(冪等性\)   \*\*\*\*\*很重要\*\*\*\*\*

**用於：API Design  
  
定義，什麼是Idempotent?**  
If you apply a function once to some argument, it has the same effect as applying twice or three times or any number of times.  
  
**\* Not idempotent:** increment a counter --&gt; ****$${f(likeCount) = likeCount + 1}$$   
**\* Idempotent:** add an element to a set --&gt;$${f(likeSet) = likeSet \cup userID}$$   
  
**為什麼要Idempotent?**   
目的為deduplication。使其在retry但沒ack情況下，減少系統所產生的duplication。

### 3-3. Retry Semantics

有以下幾種辦法Retry

* **At-most-once** semantics: send request, don't retry, update may not happen
* **At-least-once** semantics retry request until ack\(acknowledged\), may repeat update
* **Exactly-once** semantics retry + idemopotence or deduplication

### 3-4. Reconcile Replicas

利用timestamp來記錄先後順序，使其在inconsistent時，能自動更新。

### 3-5. Concurrent Writes by Different Clients

* Last-Writer Wins
* Multi-Value Register

