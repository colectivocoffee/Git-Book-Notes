# Interview Qs and Ans

## Design TinyUrl, bit.ly, goo.gl

Below are some stuff to keep in mind

### 1. Database Schema/Data Model Design

* Read-heavy application
* Each object we store is small \( &lt; 1k \)



| URL \(info about URL mappings\) |
| :---: |
| PK \| hash: varchar\(16\)  |
| OriginalUrl: varchar\(512\) |
| CreationDate: datetime |

| User \(info about user's data\) |
| :---: |
| PK \| UserId: int |
| Name: varchar\(20\) |
| Email: varchar\(32\) |
| CreationDate: datetime |
| LastLogin: datetime |

#### What kind of DB should we use? 

Since we are likely going to \(1\) store billions of rows \(2\) don't need to use relationships between objects.   
---&gt; **NoSQL key-value store** would be **easier to scale**.  
---&gt; NoSQL cannot store relationship between User and Url \(no foreign keys in NoSQL\)  
---&gt; we need a **third table** to store mapping between them.   

### 2. Important Concepts in Diagram - How to Encode Actual URL?

> “​http://tinyurl.com/jlg8zpc”​   &lt;--- jlg8zpc is the unique hash result  
>   
> We should ask 'what should be the length of the short key/has result? 6,8, or 10 chars?

Encoding Actual URL. We can compute **unique hash** by the following techniques

* MD5
* SHA256

