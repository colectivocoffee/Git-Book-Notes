# ACID & CAP

## ACID Meaning

**Atomicity**: something that cannot be broken into smaller parts  
**Consistency**: application specific notion of the DB beginning in a good state  
**Isolation**: if accessing same DB records at the same time, will run into concurrency problem.  
**Durability**: provide a safe place where data be stored without losing it. 

Where ACID is used?   
A lot of databases offer ACID properties. DB transactions can be effectively used to **atomically** write data while ensuring consistency, either succeed or failed as a unit.   

## CAP Theorem

consistency, availability and partition tolerance/performance/latency

**C+A** \(High Consistency, High Availability\) --&gt; 非常慢\(low perf\)  
**A+P** \(High Availability, High Performance\) --&gt; Data常常不正確\(in-consistent\)    
**C+P** \(High Consistency, High Performance\) --&gt; Server穩定度不高\(low availability\)

### Tradeoffs

<table>
  <thead>
    <tr>
      <th style="text-align:left">Tradeoffs</th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li>consistency &lt;--&gt; latency</li>
        </ul>
      </td>
      <td style="text-align:left">
        <p>Everything Consistent, High Latency</p>
        <p></p>
        <p>In-consistent, Low Latency</p>
      </td>
    </tr>
  </tbody>
</table>



