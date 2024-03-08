---
title: 'sync.Pool is coming soon'
date: 2014-05-11T12:08:00.000-07:00
draft: false
url: /2014/05/syncpool-is-coming-soon.html
---

I predict that sync.Pool, an upcoming Go 1.3 feature, will be everywhere. Everyone will know how to use it and will change their existing programs to use it.  
  
sync.Pool is a nice way to save allocations. In one example, I've [replaced](https://code.google.com/r/yvesjunqueira-bencode/source/detail?r=029262d400d76ac6f466cd0b7a7d90867beb74d8) the buffer in bencode-go (used by Taipei Torrent) with a sync.Pool and it lead to massive savings in allocations. And the resulting code isn't ugly.  
  
Readable and fast code == WIN.  
  
These are the benchmark tests for bencode. Note the drop from 64655 bytes per operation to 7998 bytes per operation, for the BenchmarkDecodeAll test.  
  

1.  Before
    
2.  $ go test -bench=. -benchmem
    
3.  PASS
    
4.  BenchmarkDecodeAll         10000            105804 ns/op           64655 B/op        186 allocs/op
    
5.  BenchmarkUnmarshalAll      10000            174444 ns/op           69304 B/op        292 allocs/op
    

7.  After
    
8.  $ go test -bench=. -benchmem
    
9.  PASS
    
10.  BenchmarkDecodeAll         50000             51194 ns/op            7998 B/op        160 allocs/op
    
11.  BenchmarkUnmarshalAll      10000            106387 ns/op           13001 B/op        266 allocs/op
    
12.  ok      code.google.com/p/bencode-go    4.147s
    

  
That said, I think it's a shame that programmers have to worry about memory allocations in Go. We still need to improve the garbage collectors. For servers with big heaps, the pauses are too long. You can avoid pauses by allocating less, but when pauses happens, they hurt. Adding more cores helps, but don't solve the problem.  
  
So I can make my second prediction: Go will add heap generations of some sort. They are a way of partitioning the heap between long-lived objects and small lived ones. So if the young generation is full, only that small heap has to be sweeped, and not the whole thing. That would be a big gain, but it's not easy to do - so it may take a while for Go to get there.
