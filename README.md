## Operating systems

* What happens when you `cat` a file in the terminal
    * Say the syscalls printed by `strace`
    * [Kernel VFS interface](https://en.wikipedia.org/wiki/Virtual_file_system)
    * Kernel I/O
    * Deffered work
    * TTY Driver
    
* [Cache Coherency](https://en.wikipedia.org/wiki/Cache_coherence)
    > In computer architecture, cache coherence is the uniformity of shared resource data that ends up stored in multiple local caches. When clients in a system maintain caches of a common memory resource, problems may arise with incoherent data, which is particularly the case with CPUs in a multiprocessing system.
* [Virtual Memory](https://bottomupcs.com/chapter05.xhtml)
    > Virtual memory is all about making use of address space.
    > The address space of a processor refers the range of possible addresses that it can use when loading and storing to memory. The address space is limited by the width of the registers, since as we know to load an address we need to issue a load instruction with the address to load from stored in a register. For example, registers that are 32 bits wide can hold addresses in a register range from 0x00000000 to 0xFFFFFFF. 2^32 is equal to 4GB, so a 32 bit processor can load or store to up to 4GB of memory.
* [TLB/MMU](https://bottomupcs.com/virtual_memory_hardware.xhtml#the_tlb)
  > The Translation Lookaside Buffer (or TLB for short) is the main component of the processor responsible for virtual-memory. It is a cache of virtual-page to physical-frame translations inside the processor. The operating system and hardware work together to manage the TLB as the system runs.
* [Interrupts/ISR](https://bottomupcs.com/peripherals.xhtml)
  > An interrupt allows the device to literally interrupt the processor to flag some information. For example, when a key is pressed, an interrupt is generated to deliver the key-press event to the operating system. Each device is assigned an interrupt by some combination of the operating system and BIOS.
* [What happens when you `fork`](https://bottomupcs.com/fork_and_exec.xhtml#d0e5739)
    * Given the next snippet describe what happens. The printf gets called twice because 
    the unflushed output buffer is inherited upon fork and flushed on exit.
    ```
    printf("Hello");
    fork();
    ```
* [CPU Scheduling strategies](https://bottomupcs.com/scheduling.xhtml)
* [Process states](https://media.geeksforgeeks.org/wp-content/uploads/transitions.jpg)
* Can you have global data specific to a thread? [Thread Local Storage](https://en.wikipedia.org/wiki/Thread-local_storage)
* Thread-Safe vs Reentrant
    * Generally, thread-safe refers to access/modification of data from different threads e.g. `vector::push_back()`.
    * Functions with respect to instances running on multiples threads can be reentrant or not e.g. `strtok`.
* [How does a SW breakpoint work?](http://man7.org/linux/man-pages/man2/ptrace.2.html)
    * The tracer attaches to the tracee via trap, halts the execution, reinserts the trap and continues. - `man 2 ptrace`
* What is a busy loop? Why is it bad? What are the alternatives?
    * `while(read(fd, buff, sizeof buff,0 ) > 0) { // Do stuff  }`
    * `poll`, `epoll`, `select`
* Difference between [`poll`](https://linux.die.net/man/2/poll) and [`epoll`](https://linux.die.net/man/4/epoll)?
    * `epoll` is Linux specific and returns only the descriptors that are ready.
    * for `poll` you have to enumerate and check all the descriptors for their state.


## Algorithms

* [Reverse a singly linked-list in place.](https://www.geeksforgeeks.org/reverse-a-linked-list/)
* Given a sequence of brackets, determine if they're closed correctly
    * Push open brackets on a stack and start poping on closing brackets. Check if they match
    and the stack is empty at the end.
* Consumer Producer Problem
```
	semaphore empty_s = 10;
	semaphore full_s  =  0;
	semaphore lock    =  1;
	
	producer() {
	    item = Produce();
	    down(empty_s);
	    down(lock);
	    putItemInQueue(item);
	    up(lock);
	    up(full_s);
	}
	
	consumer() {
	    down(full_s);
	    down(lock);
	    item = getItemFromQueue();
	    up(lock);
	    up(empty_s);
	}
```

* Stack-like data structure that keeps that also has a `getMin()` method. 
    * [Keep two stacks](https://www.geeksforgeeks.org/tracking-current-maximum-element-in-a-stack/)
    * Or have a single stack that holds a tuple <element, current minimum>.
```
    std::deque<std::tuple<int, int>> stacky; // The first element is the actual value, the second is the current minimum.
```

* Given an array, return the indexes of two members that add up to a given sum (if present)
```
    map[value_from_array] = index_of_value_from array;
    map.find(sum - some_other_value_from_array);
```

* [Implement a function that, given an integer N (1<N<100), returns an array containing N unique 
integers that sum up to 0. The function can return any such array. For example, given N=4, the 
function could return {1, 0, -3, 2} or {-2, 1, -4, 5}, but not {1, -1, 1, 3}.](https://www.techiedelight.com/find-subarrays-given-sum-array/)

## C/C++

* Implement a unique pointer in C++.
```
	template<typename T>
	class uniq;
	
	template<typename T>
	uniq<T>
	make_uniq(T elem);
	
	template<typename T>
	class uniq
	{
	
	public:
	  uniq<T>()
	    : _elem(nullptr){
	
	    };
	  uniq<T>(const uniq<T>& other) = delete;
	  uniq<T>(uniq<T>&& other)
	  {
	    this->_elem = other._elem;
	    other._elem = nullptr;
	  }
	
	  ~uniq() { delete _elem; }
	
	  friend uniq<T> make_uniq<>(T elem);
	
	private:
	  T* _elem;
	};
	
	template<typename T>
	uniq<T>
	make_uniq(T elem)
	{
	  uniq<T> a;
	  a._elem = new T(elem);
	  return a;
	}
```

* What are the differences between `std::map` and `std::unordered_map`?
    * Like the names imply `std::map`'s keys are ordered using `operator<()`.
    * `std::map` is implemented using [Red-Black tree](https://www.cs.auckland.ac.nz/software/AlgAnim/red_black.html).
    * `std::unordered_map` is implemented usign [Hash Map](https://en.wikipedia.org/wiki/Hash_table).
    ```
            Hash Table    Balanced BST
    Space     O(n)             O(n)
    Search    O(1)             O(log n)
    Insert    O(1)             O(log n)
    Delete    O(1)             O(log n)
    ```

* Implement a singly-linked list with add/delete/contains.
```
	template<typename T>
	class Listy
	{
	  T _elem;
	  Listy<T>* _next;
	
	public:
	  Listy<T>(const Listy<T>& other) = delete;
	
	  Listy<T>& operator=(const Listy<T>& other) = delete;
	
	  Listy<T>()
	    : _elem()
	    , _next(nullptr)
	  {}
	
	  ~Listy()
	  {
	    if (_next) {
	      auto i = _next;
	      while (i) {
	        auto d = i->_next;
	        i->_next = nullptr;
	        delete i;
	        i = d;
	      }
	    }
	  }
	
	  void add(T elem)
	  {
	    auto* n = new Listy<T>();
	    n->_elem = elem;
	    n->_next = _next;
	    _next = n;
	  }
	
	  bool contains(T elem)
	  {
	    if (!_next) {
	      return false;
	    }
	    for (auto i = _next; i; i = i->_next) {
	      if (i->_elem == elem) {
	        return true;
	      }
	    }
	    return false;
	  }
	
	  bool remove(T elem)
	  {
	    if (!_next) {
	      return false;
	    }
	
	    if (_next->_elem == elem) {
	      auto curr = _next;
	      _next = curr->_next;
	      curr->_next = nullptr;
	      delete curr;
	      return true;
	    }
	
	    auto it = _next;
	    while (it->_next && it->_next->_elem != elem) {
	      it = it->_next;
	    }
	
	    if (!it->_next) {
	      return false;
	    } else {
	      auto next = it->_next;
	      it->_next = next->_next;
	      next->_next = nullptr;
	      delete next;
	      return true;
	    }
	  }
	};
```

* [Diamond inheritance problem](https://www.cprogramming.com/tutorial/virtual_inheritance.html)
```
	struct Base
	{
	  virtual void deadmaus() {}
	};
	
	struct Drop : public Base
	{
	  virtual void deadmaus() override {}
	};
	
	struct The : public Base
	{
	  virtual void deadmaus() override {}
	};
	
	struct Skrillex
	  : public Drop
	  , public The
	{};
	
	int
	main()
	{
	  Skrillex bangarang;
	  bangarang.deadmaus();
	}
```

Fix it by inheriting virtually.


* [Polymorphic behaviour - Inheritance vs Composition](https://en.wikipedia.org/wiki/Composition_over_inheritance)

```
	struct Ram
	{};
	
	struct Computer
	{};
	
	// Smartphone `is-a` Computer, and `has-a` Ram.
	struct Smartphone : public Computer
	{
	  Ram r;
	};
```

* Pointer arithmethic
  * `int *a = 0; a++; printf("%p\n", a)` => 0 + sizeof(*a) = 4

* Callback mechanism in C
```
	typedef int (*my_cool_callback)(void* ctx);
	int register_callback(int event, my_cool_callback f, void* contex);
```

* [Near, Far, Huge Pointers](https://en.wikipedia.org/wiki/Intel_Memory_Model)
    * Near pointers 
    > are 16-bit offsets within the reference segment, i.e. DS for data and CS for code. They are the fastest pointers, but are limited to point to 64 KB of memory (to the associated segment of the data type). Near pointers can be held in registers (typically SI and DI).
    * Far pointers
    > are 32-bit pointers containing a segment and an offset. To use them the segment register ES is used by using the instruction les [reg]|[mem],dword [mem]|[reg]. They may reference up to 1024 KiB of memory. Note that pointer arithmetic (addition and subtraction) does not modify the segment portion of the pointer, only its offset. Operations which exceed the bounds of zero or 65535 (0xFFFF) will undergo modulo 64K operation just as any normal 16-bit operation. The moment counter becomes (0x10000), the resulting absolute address will roll over to 0x5000:0000.
    * Huge Pointers
    > are essentially far pointers, but are (mostly) normalized every time they are modified so that they have the highest possible segment for that address. This is very slow but allows the pointer to point to multiple segments, and allows for accurate pointer comparisons, as if the platform were a flat memory model: It forbids the aliasing of memory as described above, so two huge pointers that reference the same memory location are always equal.

* Is it legal for a method to call `delete this;`?
    * Valid if object was created using `new`.
    * Undefined otherwise.
    
* What is the order for evaluating function arguments in C++?
    * Unspecified

* Memory leak using smart pointers
```
	auto ptr = std::make_unique<int>(10);
	auto raw = ptr.release();
```

### Code review

```
	#include "defs.h"
	#include <stdio.h>
	
	const char *inet_ntoa(uint32_t ip) {
	
	  char buffer[IPV4_STR_SIZE];
	
	  if (ip == 0) {
	    return "0.0.0.0";
	  }
	
	  char *b = &ip;
	
	  snprintf(buffer, sizeof(buffer), "%u %u %u %u", b[0], b[1], b[2],
	           b[3]); // Endianness issues.
	
	  return buffer; // Returning a buffer declared on the stack.
	}
```
* What size does `IPV4_STR_SIZE` need to have?
    * 16
* What does it mean for a variable to be declared `static` in a function?
    * Defined in the `bss` or `data` sections of the elf.
    * Only one instance of the buffer regardless of many threads call the function.
* If the buffer was declared `static` would the function be thread-safe? 
    * No
* What are the differences between `static` and `global`?
    * `static` specifies that a variable is declared in `.bss`, doesn't have
    automatic memory allocation, is only visible to that compilation unit i.e. 
    internal linkage.
    * `global`s reside in `.data` or `.rodata` sections, have `static` lifetime,
    are initialized to a value, can be accessed from other compilation units using
    `extern` i.e. external linkage.
* If this function were to be called by a maximum of 10 threads how would 
you accomplish that?
    * Keep an array of 10 buffers, hash each thread upon function entry by thread id,
    and use corresponding buffer.

----

## Networking

* How would a packet capture thread look like?
    * [Using `epoll`]

* Routing tables
    * How does one look like?
        * Destination, Next Hop, Interface, Protocol, Metric, MetricType, State
    * What data structure would you use to implement one?
        * [Radix Trie], [Poptrie, DXR]
            * [Routing DS Comparison], [Radix Trie Explanation], [Linux RIB], [FreeBSD RIB]

* What is load-balancing?
    * Is a technique of distributing workload or information accross multiple links or to multiple processing units.
    * What is ECMP?
        * Equal Cost Multi Path is a routing technique used to forward packets to the same destination using multiple 
        routes of same metric.
    * How would you implement a hashing algorithm for ECMP?
        * `(src_mac ^ dst_mac + src_ip ^ dst_ip) % no_of_routes`

[Radix Trie]: https://en.wikipedia.org/wiki/Radix_tree
[Poptrie, DXR]: http://conferences.sigcomm.org/sigcomm/2015/pdf/papers/p57.pdf
[Routing DS Comparison]: https://pdfs.semanticscholar.org/563f/f3059cf0d0bf7d9bef0c0d17c890e47f5090.pdf
[Radix Trie Explanation]: https://trie.now.sh/
[Linux RIB]: https://vincent.bernat.ch/en/blog/2017-ipv4-route-lookup-linux
[FreeBSD RIB]: https://www.openbsd.org/papers/eurobsdcon2016-embracingbsdrt.pdf
[Using `epoll`]: https://bitbucket.org/rhadamanthus/vulny/src/e7bfc11780030d736ac2d7c766a50d7b4fdd9f6d/lib/async_io.cc#lines-138

