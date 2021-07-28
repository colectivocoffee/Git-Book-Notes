# \[Medium\] Meeting Rooms II

[Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)  
Given an array of meeting time intervals consisting of start and end times `[[s1,e1],[s2,e2],...]` \(si &lt; ei\), find the minimum number of conference rooms required.

**Example 1:**

```text
Input: [[0, 30],[5, 10],[15, 20]]
Output: 2
```

**Example 2:**

```text
Input: [[7,10],[2,4]]
Output: 1
```

## Analysis & Code

{% hint style="info" %}
這題關鍵點在於   
\(1\) **start time** -- `intervals[i][0]`   
\(2\) **end time** -- `intervals[i][1]`  
\(3\) **current earliest available room** \(by fetching min end time\) `free_rooms[0]`  
要拿到min end time最方便的辦法，便是使用**min heap**。  
  
An ending event tells us that a previously occupied room has now become free.  
因此我們要拿每個start time和`free_rooms[0]`比較，如果`free_rooms[0]`比較大，代表連最早有可能空出位置來的`free_rooms`都滿了，我們只能開闢一個新房間，即heappop\(min heap\)。
{% endhint %}

A naive way to check if a room is available or not is to iterate on all the rooms and see if one is available when we have a new meeting at hand. However, we can do better than this by making use of Priority Queues or the Min-Heap data structure.

The first part of the tuple is the start time for the meeting and the second value represents the ending time. We are considering the meetings in sorted order of their start times. The first diagram depicts the first three meetings where each of them requires a new room because of collisions.

![](../../.gitbook/assets/image%20%2888%29.png)

The next 3 meetings start to occupy some of the existing rooms. However, the last one requires a new room altogether and overall we have to use 4 different rooms to accommodate all the meetings.

![](../../.gitbook/assets/image%20%2894%29.png)



### **1. Chronological Ordering: O\(nlogn\) / O\(n\)**

![](../../.gitbook/assets/image%20%2891%29.png)

![](../../.gitbook/assets/image%20%2889%29.png)

Time Complexity: `O(nlogn)` sorting two arrays need `nlogn` time.  
Space Complexity: `O(n)`  Extra two arrays N, start&end are needed.

```python
def minMeetingRooms(self, intervals: List[List[int]]) -> int:

    if len(intervals) == 0:
        return 0
    
    all_start = []
    all_end = []
    
    # create the list of all start & end
    for i in range(len(intervals)):
        start = intervals[i][0] 
        end = intervals[i][1]
        all_start.append(start)
        all_end.append(end)
    
    # sort them to be used for later intervals iteration
    all_start.sort()
    all_end.sort()
    
    rooms = 0  
    end_id = 0
    for i in range(len(intervals)):
        # ..[0-20]....
        # ...[10-30]..
        # if end time overlaps with start time, then we need an extra meeting room.
        if all_start[i] < all_end[end_id]:
            rooms += 1
        #  ..[0-20]..[30-40]..
        # if there's vacant time between start&end, 
        # meaning there's a meeting room available to be used, just update endtime.
        else:
            end_id += 1 
```

### 2. min Heap:    O\(NlogN\) / O\(N\)

```python
def minMeetingRooms(self, intervals: List[List[int]]) -> int:

    if len(intervals) == 0:
        return 0
        
    # sort all meetings by start time
    intervals.sort(key = lambda x: x[0])
    
    # construct heap
    all_ends = []
    
    # use heapq to get latest end time (already done by min heap)
    for meeting in intervals:
        
        curr_start_time = meeting[0]
        curr_end_time = meeting[1]
        if all_ends and curr_start_time >= all_ends[0]:
            # meaning two meetings can use same room (with >= all_ends time)
            # heapreplace == heappop + heappush
            heapq.heapreplace(all_ends, curr_end_time)
        else:
            # otherwise add a meeting room by current meeting end time
            heapq.heappush(all_ends, curr_end_time)
    
    # remaining end times are eqivalent to # of meeting rooms required.
    return len(all_ends)    
        
    
```

### 2. min Heap2 \(Preferred\): O\(NlogN\) / O\(N\)

```python
import heapq
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        
        # sort by begin time, O(nlogn)
        intervals = sorted(intervals, key = lambda x:x[0])
        
        # min heap, to retrieve earliest available time by end time 
        free_rooms = []
        
        # first end time
        first_end = intervals[0][1]
        # minHeap存的是end_time_for_latest_meeting
        heapq.heappush(free_rooms, first_end)
        
        for item in intervals[1:]: #從第二個開始
            
            # 所有房間已滿, 
            # meaning end time is greater than first start time on min heap
            # item[0]是meeting start time
            if item[0] >= free_rooms[0]: 
                heapq.heappop(free_rooms)
            
            # update free rooms by end time
            # item[1]是meeting end time
            heapq.heappush(free_rooms, item[1])
        
        return len(free_rooms)
```

## 



