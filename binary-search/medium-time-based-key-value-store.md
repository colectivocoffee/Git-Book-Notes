# \[Medium\] Time Based Key-Value Store

## Time Based Key-Value Store \(1479/166\)

Design a time-based key-value data structure that can store multiple values for the same key at different time stamps and retrieve the key's value at a certain timestamp.

Implement the `TimeMap` class:

* `TimeMap()` Initializes the object of the data structure.
* `void set(String key, String value, int timestamp)` Stores the key `key` with the value `value` at the given time `timestamp`.
* `String get(String key, int timestamp)` Returns a value such that `set` was called previously, with `timestamp_prev <= timestamp`. If there are multiple such values, it returns the value associated with the largest `timestamp_prev`. If there are no values, it returns `""`.

**Constraints:**

* `1 <= key.length, value.length <= 100`
* `key` and `value` consist of lowercase English letters and digits.
* `1 <= timestamp <= 107`
* **All the timestamps `timestamp` of `set` are strictly increasing.   &lt;--- 要問面試官，關鍵**
* At most `2 * 105` calls will be made to `set` and `get`.

### 1. Dict + Linear Search: O\(n\) / O\(n\)

### 2. Dict + Binary Search\(bisect\): O\(logn\) / O\(n\) 

```python
class TimeMap:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.times = collections.defaultdict(list)
        self.values = collections.defaultdict(list)
        

    def set(self, key: str, value: str, timestamp: int) -> None:
        self.times[key].append(timestamp)
        self.values[key].append(value)

    def get(self, key: str, timestamp: int) -> str:
                
        idx = bisect.bisect(self.times[key], timestamp)
        return self.values[key][idx - 1] if idx else ''
        
        
# Your TimeMap object will be instantiated and called as such:
# obj = TimeMap()
# obj.set(key,value,timestamp)
# param_2 = obj.get(key,timestamp)
```

