Contents
========

* `Operating Systems`_
* `Algorithms`_
* `C/C++`_
* `Code Review`_
* `Networking`_



Operating Systems
=================

* Do threads have their own stack and why? (Ixia)

    Yes. The stack of each thread contains particular automatic lifetime data
    which is exclusively relevant for methods in a thread's workflow.
    Each function has its own arguments and return addresses which are specific
    to the work of a thread.

* What happens when you ``cat`` a file in the terminal? (Amazon)

  * Say the syscalls printed by ``strace``
  * `Kernel VFS interface <https://en.wikipedia.org/wiki/Virtual_file_system>`_
  * Kernel I/O
  * Deffered work
  * TTY Driver

* `Cache Coherency <https://en.wikipedia.org/wiki/Cache_coherence>`_ (Amazon)

    In computer architecture, cache coherence is the uniformity of
    shared resource data that ends up stored in multiple local caches.
    When clients in a system maintain caches of a common memory resource,
    problems may arise with incoherent data, which is particularly the case
    with CPUs in a multiprocessing system.

* `Virtual Memory <https://bottomupcs.com/chapter05.xhtml>`_

    Virtual memory is all about making use of address space.

    The address space of a processor refers the range of possible addresses
    that it can use when loading and storing to memory.
    The address space is limited by the width of the registers, since as we know
    to load an address we need to issue a load instruction with the address to
    load from stored in a register.
    For example, registers that are 32 bits wide can hold addresses in a
    register range from 0x00000000 to 0xFFFFFFF.
    2^32 is equal to 4GB, so a 32 bit processor can load or store to
    up to 4GB of memory.

* `TLB/MMU <https://bottomupcs.com/virtual_memory_hardware.xhtml#the_tlb>`_

    The Translation Lookaside Buffer (or TLB for short) is the main component
    of the processor responsible for virtual-memory.
    It is a cache of virtual-page to physical-frame translations inside the
    processor.
    The operating system and hardware work together to manage
    the TLB as the system runs.

* `Interrupts/ISR <https://bottomupcs.com/peripherals.xhtml>`_ (Tremend/NXP)

    An interrupt allows the device to literally interrupt the processor to
    flag some information.
    For example, when a key is pressed, an interrupt is generated to deliver
    the key-press event to the operating system.
    Each device is assigned an interrupt by some combination of the operating
    system and BIOS.

* `What happens when you fork?
  <https://bottomupcs.com/fork_and_exec.xhtml#d0e5739>`_ (Intel)

  * Given the next snippet describe what happens.
    The ``printf`` string appears twice because the unflushed output buffer
    is inherited upon fork and flushed on exit. (Luxoft)

    .. code-block:: c

       printf("Hello");
       fork();

* `CPU Scheduling strategies <https://bottomupcs.com/scheduling.xhtml>`_
  (Luxoft)

* `Process states
  <https://media.geeksforgeeks.org/wp-content/uploads/transitions.jpg>`_
  (Luxoft)

* Can you have global data specific to a thread? (Luxoft)

  * `Thread Local Storage <https://en.wikipedia.org/wiki/Thread-local_storage>`_

* Thread-Safe vs Reentrant (Luxoft)

  * Generally, thread-safe refers to access/modification of data from
    different threads e.g. ``vector::push_back()``.

  * Functions with respect to instances running on multiples threads can
    be reentrant or not e.g. ``strtok``.

* `How does a SW breakpoint work
  <http://man7.org/linux/man-pages/man2/ptrace.2.html>`_? (Luxoft)

  * The tracer attaches to the tracee via trap, halts the execution,
    reinserts the trap and continues. - ``man 2 ptrace``

* What is a busy loop? Why is it bad? What are the alternatives? (Tellence)

  * ``while(read(fd, buff, sizeof buff,0 ) > 0) { /* Do stuff */  }``

  * ``poll``, ``epoll``, ``select``

* Difference between `poll <https://linux.die.net/man/2/poll>`_
  and `epoll <https://linux.die.net/man/4/epoll>`_ ?
  (Tellence/Societe Generale) ?

  * ``epoll`` is Linux specific and returns only the descriptors that are ready.

  * for ``poll`` you have to enumerate and check all the descriptors
    for their state.

* What does Level-Trigger and Edge-Trigger means when refering to ``epoll``?
  (Societe Generale)

  * ``man 2 epoll``

    * Edge triggering means that epoll will notify of IO only once.
      If we do *some* of that IO, but not finish it, ``epoll`` will block
      until another descriptor becomes ready.

    * Level triggering implies the oposite i.e. as long as there ready
      IO operations ``epoll`` will notify us.

* What is `DMA
  <https://docs.freebsd.org/doc/2.1.5-RELEASE/usr/share/doc/handbook/handbook245.html>`_?

    Direct Memory Access (DMA) is a method of allowing data to be moved from
    one location to another in a computer without intervention
    from the central processor (CPU).

  * A MCU can map peripherals' registers to the address space
    in order to communicate directlty.

* What does it mean for a function to be async signal safe?

  * ``man 7 signal-safety``

      An async-signal-safe function is one that can be safely called from
      within a signal handler.
      Many functions are not async-signal-safe.
      In particular, nonreentrant functions are generally unsafe to call
      from a signal handler.

  * Reentrant, Non-blocking

* Whats is the general structure of kernel module that registers
  a character device? (NXP)

  * allocate device entry ``alloc_chrdev_region``
  * bind device to file operations ``cdev_init``
  * add your character device to the dev tree ``cdev_add``
  * in userspace call ``mknod`` to actually create the device in ``/dev/``

* Whenever we ``read``/``write``/``seek`` a device what is being called
  in the kernel? (NXP)

  * `VFS(Virtual File System)
    <https://elixir.bootlin.com/linux/v5.4.14/source/include/linux/fs.h#L1821>`_

* If you dereference a pointer in kernel is that address virtual or physical?
  (NXP)

  * Virtual

* Can you get - and if yes, how - the actual physical address from a
  virtual one? (NXP)

  * Yes, `virt_to_phys
    <https://elixir.bootlin.com/linux/latest/source/include/asm-generic/io.h>`_.
  * NOTE - if you try to dereference it the kernel still treats it as virtual.

* Can someone write directly at a physical address? If yes, how? (NXP)

  * First that memory region mustn't be managed by the kernel.
  * We must remap that physical address into virtual space using `ioremap
    <https://elixir.bootlin.com/linux/latest/source/arch/x86/include/asm/io.h>`_.



Algorithms
==========

* You are given a matrix consisting of leaves represented by positive integers and a string of
  wind directions. Any of the four directions moves the leaves one position. Once any leaf is out,
  an opposite wind won't bring it back. Write an algorithm that computes the number of remaining leaves
  after a series of wind gusts. (Gameloft) Example:

  ::

     1 0 0 0
     2 0 2 0
     0 1 1 0
     0 0 0 0

     RRDD -> 3
     LLLL -> 0
     RRLL -> 4
     UULL -> 1

  .. code-block:: cpp

     int
     count_leaves(std::vector<std::vector<int>> grid, std::string winds)
     {
         ssize_t max_right {}, max_left {}, max_up {}, max_down {};
         ssize_t cur_up {}, cur_left {};

         const size_t height = grid.size();
         const size_t width  = grid.front().size();

         for (auto w : winds) {
             if (w == 'L') {
                 ++cur_left;
                 if (cur_left > max_left) {
                     max_left = cur_left;
                     if (max_left == width)
                         return 0;
                 }
             }
             if (w == 'R') {
                 --cur_left;
                 if (-cur_left > max_right) {
                     max_right = -cur_left;
                     if (max_right == width)
                         return 0;
                 }
             }
             if (w == 'U') {
                 ++cur_up;
                 if (cur_up > max_up) {
                     max_up = cur_up;
                     if (max_up == height)
                         return 0;
                 }
             }
             if (w == 'D') {
                 --cur_up;
                 if (-cur_up > max_down) {
                     max_down = -cur_up;
                     if (max_down == height)
                         return 0;
                 }
             }
         }

         for (size_t i = 0; i < height; ++i) {
             for (size_t j = 0; j < width; ++j) {
                 if (j < max_left)
                     grid.at(i).at(j) = 0;

                 if (j >= width - max_right)
                     grid.at(i).at(j) = 0;

                 if (i < max_up)
                     grid.at(i).at(j) = 0;

                 if (i >= height - max_down)
                     grid.at(i).at(j) = 0;
             }
         }

         size_t res {};
         for (size_t i = 0; i < height; ++i)
             for (size_t j = 0; j < width; ++j)
                 res += grid.at(i).at(j);

         return res;
     }



* Implement ``strcmp``. (Arista)

  .. code-block:: cpp

     int
     my_strcmp(const char *s1, const char *s2)
     {
         while (*s1 || *s2) {
             if (*s1 < *s2) {
                 return -1;
             } else if (*s1 > *s2) {
                 return 1;
             }
             s1++;
             s2++;
         }
         return 0;
     }



* Merge two sorted linked lists (Arista)

  .. code-block:: cpp

     struct node
     {
         struct node *next;
         int          val;
     };

     node *
     merge(struct node *a, struct node *b)
     {
         struct node head = { 0 }, *it = &head;

         while (a && b) {
             if (a->val < b->val) {
                 it->next = a;
                 a        = a->next;
             } else {
                 it->next = b;
                 b        = b->next;
             }
             it = it->next;
         }

         if (a) {
             it->next = a;
         } else if (b) {
             it->next = b;
         }

         return head.next;
     }



* You are given a sorted array of N-1 positive integers from 1 to N-1. Find the
  missing integer in O(logn). (Arista)

  .. code-block:: cpp

     int
     find(int *arr, size_t sz)
     {
         size_t left  = 0;
         size_t right = sz - 1;

         while (left < right) {
             size_t mid = left + (right - left) / 2;

             if (arr[mid] - arr[0] == mid)
                 left = mid + 1;
             else
                 right = mid;
         }

         return arr[left] - 1;
     }



* Write a function that takes a positive integer N and counts all the numbers
  that raised to the power of N have exactly N digits. (Teramind)

  .. code-block:: cpp

     int
     count_digits(int num)
     {
         int ct {};
         while (num) {
             ++ct;
             num /= 10;
         }
         return ct;
     }

     int
     find_integers(int n)
     {
         int count {};
         int x { 1 };

         while (true) {
             int power  = std::pow(x, n);
             int digits = count_digits(power);

             if (digits == n) {
                 count++;
             } else if (digits > n) {
                 break;
             }

             x++;
         }

         return count;
     }


* Remove all the odd numbers from a vector in place O(N). (Societe Generale)

  .. code-block:: cpp

     void
     rem_odd(std::vector<unsigned>& v)
     {
       if (v.empty()) {
         return;
       }

       size_t s = 0;
       size_t e = v.size() - 1;
       while (s != e) {
         while (v[s] % 2 == 0 && s < v.size()) {
           s++;
         }
         if (s == v.size()) {
           return;
         }
         while (v[e] % 2 != 0 && e > s) {
           e--;
         }
         if (e == s) {
           break;
         }
         std::swap(v[s], v[e]);
       }
       v.resize(e);
     }

  * The idea is to move all the odd elements at the end of the vector by
    swapping every odd number from the left with every even number from
    the right.


* `Reverse a singly linked-list in place.
  <https://www.geeksforgeeks.org/reverse-a-linked-list>`_
  (Bitdefender/Societe Generale)

  .. code-block:: cpp

     template<typename T>
     Listy<T>*
     reverse(Listy<T>* prev, Listy<T>* curr)
     {
       if (!curr) {
         return prev;
       }
       auto t = curr->next;
       curr->next = prev;
       return reverse(curr, t);
     }


* Given a sequence of brackets, determine if they're closed correctly
  (Bitdefender)

  * Push open brackets on a stack and start poping on closing brackets.
    Check if they match and the stack is empty at the end.

* Consumer Producer Problem (Bitdefender)

  .. code-block::

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


* Stack-like data structure that keeps that also has a ``getMin()`` method.
  (Tellence)

  * `Keep two stacks
    <https://www.geeksforgeeks.org/tracking-current-maximum-element-in-a-stack>`_
  * Or have a single stack that holds a tuple <element, current minimum>.

    .. code-block:: cpp

        // The first element is the actual value,
        // the second is the current minimum.
        std::deque<std::tuple<int, int>> stacky;


* Given an array, return the indexes of two members that add up to a given sum
  (NXP/Ixia)

  .. code-block:: cpp

     map[value_from_array] = index_of_value_from array;
     map.find(sum - some_other_value_from_array);

* Implement a function that, given an integer N (1<N<100),
  returns an array containing N unique integers that sum up to 0.
  The function can return any such array. For example, given N=4, the
  function could return {1, 0, -3, 2} or {-2, 1, -4, 5}, but not {1, -1, 1, 3}.
  `Code <https://www.techiedelight.com/find-subarrays-given-sum-array>`_
  (Amazon)

* `Add two numbers without using arithmetic operators
  <https://www.geeksforgeeks.org/add-two-numbers-without-using-arithmetic-operators>`_
  (Luxoft)

* Given a binary tree with the following format write a method that finds the
  most recent common ancestor

  .. code-block:: cpp

     struct Node
     {
         Node *parent; // nullptr for root
     };

  ::

    //      1
    //     / \
    //    2   3
    //   / \   \
    //  4   5   6
    //     / \
    //    7   8
    //
    //   5 & 6 -> 1
    //   4 & 8 -> 2
    //   7 & 8 -> 5


  .. code-block:: cpp

     Node *
     getMostRecentCommonAncestor(Node *first, Node *second)
     {
         if (!(first && second)) {
             return {};
         }

         std::unordered_set<Node *> parents;

         while (first) {
             parents.insert(first->parent);
             first = first->parent;
         }

         while (second) {
             auto parent = second->parent;
             if (parents.count(parent)) {
                 return parent;
             }
             parents.insert(parent);
             second = parent;
         }

         return {};
     }

*  Given a string that contains alphanumeric characters, dashes, and spaces
   format it so that it ends up as groups of three characters followed
   by a space. The last group can have two or three characters. If the last
   group has just one character transform the last two groups of three and one
   in two groups of two and two. (Arista Networks)

   ::

     "ABAHDB --- 12843 -"  -> "ABA HDB 128 43"
     "AB--AHDB - 128430 -" -> "ABA HDB 128 430"
     "AB--AHDB - 1284 -"   -> "ABA HDB 12 84"

   .. code-block:: cpp

      string
      FormatString(string s)
      {
          stringstream ss, rez;

          for (auto c : s) {
              if (isalnum(c)) {
                  ss << c;
              }
          }

          string end;
          size_t endpos {};

          if (ss.str().size() > 3 && ss.str().size() % 3 == 1) {
              endpos = 4;
              end    = ss.str().substr(ss.str().size() - endpos, string::npos);
          }

          for (size_t i = 0; i < ss.str().size() - endpos; ++i) {
              rez << ss.str()[i];
              if ((i + 1) % 3 == 0) {
                  rez << " ";
              }
          }

          if (end.size()) {
              rez << end[0] << end[1] << " " << end[2] << end[3];
          }

          return rez.str();
      }



C/C++
=====

* What operators cannot be overloaded? (Gameloft)

  * `::`     -- Scope Resolution Operator
  * `?:`     -- Ternary or Conditional Operator
  * `.`      -- Member Access or Dot operator
  * `.*`     -- Pointer-to-member Operator
  * `sizeof` -- Object size Operator
  * `typeid` -- Object type Operator (typeid)
  * `static_cast`
  * `const_cast`
  * `reinterpret_cast`
  * `dynamic_cast`

* Can a virtual function be called from a constructor? (Ixia/Harman)

    The virtual call mechanism is disabled in constructors and destructors.

    Typically calling virtuals from ctors and dtors is not recommended because
    it does not yield the expected behaviour.

    Derived classes are constructed from the first derived class in chain to the
    last one. The problem is that when the first class is constructed its
    `vtable` cannot point to overridden methods from classes which have not been
    created.

* Can you throw an exception from a destructor? (Ixia/Harman)

    Before C++11 it was possible to do it, but there were high risks of triggering
    an abort. If a random exception was triggered at runtime and during stack
    unwinding an object would throw an exception from its destructor there would
    be no `catch` block to handle this error, thus generating an abort.

    Starting with C++11 all destructors are implictly declared as `noexcept`,
    automatically trigerring an abort.

* Write a method that returns incremental `int` values without taking arguments.
  (Ixia)

  .. code-block:: cpp

     int
     generator()
     {
       static std::atomic<int> gen;
       return gen++;
     }

* Differences between ``unique_ptr`` and ``shared_ptr``.

  * Besides the obvious name implications...

  * ``unique_ptr`` cannot be copied, only moved

* Implement a unique pointer in C++. (Ixia)

  .. code-block:: cpp

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

       uniq<T>& operator=(uniq<T>&& other)
       {
         this->_elem = other._elem;
         other._elem = nullptr;
         return *this;
       }

       uniq<T>(uniq<T>&& other)
       {
         this->_elem = other._elem;
         other._elem = nullptr;
       }

       const T& operator*() const { return *_elem; }

       const T* operator->() const { return _elem; }

       uniq<T>(const uniq<T>&) = delete;

       uniq<T>& operator=(const uniq<T>&) = delete;

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


* What are the differences between ``std::map`` and ``std::unordered_map``?
  (Tellence)

  * Like the names imply ``std::map`` keys are ordered using ``operator<()``.
  * ``std::map`` is implemented using `Red-Black tree
    <https://www.cs.auckland.ac.nz/software/AlgAnim/red_black.html>`_.
  * ``std::unordered_map`` is implemented usign `Hash Map
    <https://en.wikipedia.org/wiki/Hash_table>`_.


    ======    ===========    ============
      OP       Hash Table    Balanced BST
    ======    ===========    ============
    Space     O(n)             O(n)
    Search    O(1)             O(log n)
    Insert    O(1)             O(log n)
    Delete    O(1)             O(log n)
    ======    ===========    ============


* Implement a singly-linked list with add/delete/contains.(Ixia)

  .. code-block:: cpp


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

* `Diamond inheritance problem
  <https://www.cprogramming.com/tutorial/virtual_inheritance.html>`_
  (Bitdefender/Ixia/Viavi)

  .. code-block:: cpp

     struct Skrillex
     {
       virtual void deadmaus() {}
     };

     struct The : public Skrillex
     {
       virtual void deadmaus() override {}
     };

     struct Base : public Skrillex
     {
       virtual void deadmaus() override {}
     };

     struct Drop
       : public The
       , public Base
     {};

     int
     main()
     {
       Drop base;
       base.deadmaus();
     }

  * Fix it by inheriting virtually.


* `Polymorphic behaviour - Inheritance vs Composition
  <https://en.wikipedia.org/wiki/Composition_over_inheritance>`_
  (Viavi)

  .. code-block:: cpp

     struct Ram
     {};

     struct Computer
     {};

     // Smartphone `is-a` Computer, and `has-a` Ram.
     struct Smartphone : public Computer
     {
       Ram r;
     };

* Write a function that correctly sums up all elements in an array. (Tremend)

  .. code-block:: c

     int_least64_t
     do_sum(size_t sz, int arr[sz])
     {
       if (!(arr && sz)) {
         return 0;
       }
       int_least64_t sum = 0;
       for (size_t i = 0; i < sz; i++) {
         const int_least64_t val = arr[i];
         if (val > 0) {
           if (INT_LEAST64_MAX - val < sum) {
             errno = ERANGE;
             break;
           }
         } else if (val < 0) {
           if (INT_LEAST64_MIN - val > sum) {
             errno = ERANGE;
             break;
           }
         }
         sum += val;
       }
       return sum;
     }

* Write a template that takes a type and a number and generates number-times pointer to type. (Tremend)

  .. code-block:: cpp

     template<typename T, size_t N>
     struct MyStruct
     {
         using type = typename MyStruct<T *, N - 1>::type;
     };

     template<typename T>
     struct MyStruct<T, 0>
     {
         using type = T;
     };

     int
     main()
     {
         // write a template with the following property
         MyStruct<int, 2>::type   i; // generates an int ** variable named i
         MyStruct<float, 1>::type f; // generates a float * variable named f
     }

* Populate a binary tree given two rules then find element in tree

  .. code-block:: cpp

     struct Tree
     {
         using Node = Tree *;

         Node left {}, right {};
         int  val;

         Tree(int val) : val(val) {}

     };

     struct Solution
     {
         Tree *root {};

         void
         dt(Tree *current, int value)
         {
             if (!current) {
                 return;
             } else {
                 current->val = value;
                 dt(current->left, 2 * value + 1);
                 dt(current->right, 2 * value + 2);
             }
         }

         // given the root of another tree, save that pointer locally and update it
         // using the following rules:
         // root->value = 0;
         // left        = 2 * parent->val + 1;
         // right       = 2 * parent->val + 2;
         void
         populate(Tree *other)
         {
             root = other;
             dt(root, 0);
         }

         //    0
         //  1    2
         //     5   6
         //
         bool
         contains(int val)
         {
             if (!root)
                 return false;

             vector<bool> path;

             while (val) {
                 if (val % 2) {
                     path.push_back(false);
                     val = (val - 1) / 2;
                 } else {
                     path.push_back(true);
                     val = (val - 2) / 2;
                 }
             }

             auto tmp = root;
             for (auto it = path.rbegin(); it != path.rend() && tmp; ++it) {
                 if (*it == true) {
                     tmp = tmp->right;
                 } else {
                     tmp = tmp->left;
                 }
             }

             if (!tmp) {
                 return false;
             } else {
                 return true;
             }
         }
     };


* Pointer arithmethic (Luxoft/NXP)

  * ``int *a = 0; a++; printf("%p\n", a)``

    * ``0 + sizeof(*a) = 4``

* Callback mechanism in C (Luxoft)

  .. code-block:: cpp


     typedef int (*my_cool_callback)(void* ctx);
     int register_callback(int event, my_cool_callback f, void* contex);


* `Near, Far, Huge Pointers <https://en.wikipedia.org/wiki/Intel_Memory_Model>`_
  (Luxoft)

  * Near pointers

      are 16-bit offsets within the reference segment, i.e. DS for data and CS for code. They are the fastest pointers, but are limited to point to 64 KB of memory (to the associated segment of the data type). Near pointers can be held in registers (typically SI and DI).

  * Far pointers

      are 32-bit pointers containing a segment and an offset. To use them the segment register ES is used by using the instruction les [reg]|[mem],dword [mem]|[reg]. They may reference up to 1024 KiB of memory. Note that pointer arithmetic (addition and subtraction) does not modify the segment portion of the pointer, only its offset. Operations which exceed the bounds of zero or 65535 (0xFFFF) will undergo modulo 64K operation just as any normal 16-bit operation. The moment counter becomes (0x10000), the resulting absolute address will roll over to 0x5000:0000.

  * Huge Pointers

      are essentially far pointers, but are (mostly) normalized every time they are modified so that they have the highest possible segment for that address. This is very slow but allows the pointer to point to multiple segments, and allows for accurate pointer comparisons, as if the platform were a flat memory model: It forbids the aliasing of memory as described above, so two huge pointers that reference the same memory location are always equal.

* Is it legal for a method to call ``delete this;``? (Luxoft)

  * Valid if object was created using ``new``.

  * Undefined otherwise.

* What is the order for evaluating function arguments in C++? (Luxoft)

  * Unspecified

* Memory leak using smart pointers (Luxoft)

  .. code-block:: cpp

     auto ptr = std::make_unique<int>(10);
     auto raw = ptr.release();

* Reference collapsing -
  **NOTE** that it might not be correct (Societe Generale)

  .. code-block:: cpp

     template<typename T>
     void
     funky(T&& t)
     {}

     struct SomeStruct
     {};

     int
     main()
     {
       // Remember & && / && & => collapse to &
       // && && => collapses to &&

       funky(5); // 5 is rval => T = int / t = int&&

       char c = 10;
       funky(c); // c is lval => T = char& / t = char& &&

       char& c_ref = c;
       funky(c_ref); // c_ref is lval& => T = char& / t = char& &&

       funky(double(5)); // double(5) is rval => T = double / t = double&&

       // SomeStruct() is rval => T = SomeStruct / t = SomeStruct&&
       funky(SomeStruct());

       SomeStruct s;
       funky(s); // s in lval => T = SomeStruct& / t = SomeStruct& &&

       funky(std::move(s)); // s is rval& => T = SomeStruct&& / t = SomeStruct&& &&
     }

* What's the meaning of const keyword at the end of a function? (Luxoft,Harman)

    A const function, denoted with the keyword ``const`` after a function
    declaration, makes it a compiler error for this class function to
    change a member variable of the class.
    However, reading of a class variables is okay inside of the function,
    but writing inside of this function will generate a compiler error.

* What's the output of this ?  (Luxoft,Harman)

  .. code-block:: cpp

     std::cout << 25u - 50;

  *  `2^32 - 25`

     * The expression causes an *integer wraparound* when we try to subtract
       a number from an unsigned of value zero, practically obtaining
       ``UNSIGNED_MAX - value``.

* How many times will this loop execute?  (Luxoft,Harman)

  .. code-block:: cpp

     unsigned char half_limit = 150;
     for (unsigned char i = 0; i < 2 * half_limit; ++i)
     {
      // do smth
     }

  * This code will result in an infinite loop.

    * The expression ``2 * half_limit`` will get promoted to an ``int``
      (based on C++ conversion rules) and will have a value of 300.
      However, since ``i`` is an unsigned char, it is represented by an 8-bit
      value which, after reaching 255, will overflow (so it will go back to 0)
      and the loop will therefore go on forever.

* What's the output of this code? Does it differ on 32 v 64 bit? (Harman)

  .. code-block:: cpp

     struct S
     {
         int32_t a, b;
     };

     int
     main()
     {
         int32_t a = 1; // top of stack - 4
         int32_t b = 3; // top of stack

         S *s = reinterpret_cast<S *>(&a); // we get a's adress
         std::cout << s->b; // and we print the second int32 in the struct
     }

  - the output should be ``3`` if we don't take into consideration optimizations
  - when using clang or ``-O3`` it spews garbage so i guess we're doing something
    fishy here -- but what?
  - there is no difference between 32 and 64 bit (??)

* Discussion around exceptions (Harman)

  * When would you use them

    Constructors, or scenarios where one cannot return an error code
    or signal somehow else that the function did not execute correctly

  * What are some alternatives

    .. code-block:: cpp

       std::optional<int>
       getA()
       {
           return {}; // for error -- not too descriptive
       }

       std::variant<int, runtime_error>
       getB()
       {
           return { std::runtime_error("some problem") }; // for error
       }

       std::tuple<int, int>
       getC()
       {
           return { {}, 10 }; // where the first value is the correct value, and the
                              // second is the error if any
       }


  * What are some disadvantages?

    - if there's no ``catch`` block your code will crash the application
    - if our methods throw inside a destructor we might trigger an abort,
      or end up with memory leaks
    - there are also considerations regarding performance -- exceptions are
      rather costly

  * How can you generate a memory leak using exceptions

    .. code-block:: cpp

       void
       leak1()
       {
           throw new int { 10 };
           // it's bad behaviour to throw heap allocated exceptions
           // you can correct this by catching an int* and calling delete on it
       }

       void
       leak2()
       {
           auto a = new int {};
           throw "leak";

           // one should use smart pointers whenever possible
           // or use a local try-catch block and delete elements in the catch --
           // error-prone
       }


* What is the name of the technique when one acquires resources upon construction
  and releases them when destructing? (Harman)

  RAII - Resource Acquisition Is Initialization

* Write a template function that return the size of the template parameter (Harman)

  .. code-block:: cpp

     template<typename T>
     size_t
     size()
     {
         return sizeof(T);
     }


  - How can you make it return ``0`` for double -- using template specialization

    .. code-block:: cpp

       template<>
       size_t
       size<double>()
       {
           return 0;
       }

* Given the following snippet, fix it to meet the requirements (Harman)

  .. code-block:: cpp

     void
     run(char c)
     {
         while (true) {
             std::cout << c;
         }
     }

     int
     main()
     {
         std::thread first(&run, 'a');
         std::thread second(&run, 'b');
         std::thread third(&run, 'c');
         third.join();
         return 0;
     }

  - Expected output of the program is: abcabcabcabc...
  - Each symbol (a,b,c) must be printed from different thread
  - Each thread must run them same ``run`` function
  - The signature of the ``run`` may vary from initial one
  - Number of threads and symbols will increase in the future


  .. code-block:: cpp

     void
     run(char c, size_t current)
     {
         static std::mutex               m;
         static std::condition_variable  cv;
         static std::atomic<std::size_t> self {}, limit {};

         limit++;

         while (true) {
             {
                 std::unique_lock<mutex> lk(m);
                 cv.wait(lk, [&] {
                     return self == current;
                 });
                 std::cout << c;
                 self = (self + 1) % limit;
             }
             cv.notify_all();
         }
     }

     int
     main()
     {
         std::vector<std::thread> threads;

         threads.emplace_back(&run, 'a', 0);
         threads.emplace_back(&run, 'b', 1);
         threads.emplace_back(&run, 'c', 2);
         threads.emplace_back(&run, 'd', 3);

         threads.back().join();

         return 0;
     }



Code Review
===========

* Harman

  .. code-block:: cpp

     struct S
     {
         int a;
     };

     int
     main()
     {
         double a = 2.6;
         int    b = static_cast<int>(a);
         S      c { b };

         int d = static_cast<int>(c);
         std::cout << b << ' ' << d;
     }

  - The code won't compile because you cannot cast a structure to a primitive without
    implementing a conversion operator such as:

    .. code-block:: cpp

       operator int()
       {
           return a;
       }

* Societe Generale

  .. code-block:: cpp

     class A
     {
     public:
       A(int m)
         : m_v(new char[m_n])
         , m_n(n)
       {}

       ~A() { delete m_v; }

     private:
       int m_n;
       char* m_v;
     };

     class B : public A
     {
     public:
       B(int n)
         : m_v(new char[n])
       {}

       ~B() { delete m_v; }
     };

     int
     main()
     {
       A* a1 = new B;
       A* a2 = new A;

       *a1 = *a2;

       delete a1;
       delete a2;
     }

  * Destructors **must** be virtual when inheriting.

  * The constructors/destructors are not declared public.

  * The constructor list of ``A`` is in the wrong order. It should be in
    the order of declaration of the fields.

    * Note that even though we call ``new char[m_n]`` despite that ``m_n``
      hasn't been initialized it, it is correct because the list initializer
      works in the order on declaration.

  * Given that we've defined a constructor with parameters for ``A``, the
    default one gets deleted and we must call it explicitly in the list
    initializer of ``B``.

  * The destructors of ``A`` and ``B`` must call ``delete[]`` because
    `m_v` is an array.

  * In ``main`` we're not passing any arguments when calling ``new A`` and
    ``new B``.

  * We try to copy ``a2`` over ``a1`` but we're using the default copy
    operator that leads to a shallow copy implying a memory leak.

    * Implement the copy constructor/operator. *Be cautious of the case when
      we try to copy over ourselves!*

      .. code-block:: cpp

         A& operator=(const class A& other)
         {
           if (this == &other) {
             return *this;
           }
           m_n = other.m_n;
           delete[] m_v;
           m_v = new char[m_n];
           memcpy(m_v, other.m_v, m_n);
           return *this;
         }

  * Deleting ``a2`` would cause a core dump because we are attempting to
    double free ``m_v``.



  .. code-block:: cpp

     struct SomeStruct
     {
       SomeStruct() { std::cout << "In Default Constructor\n"; };

       SomeStruct(const SomeStruct& other) { std::cout << "In Copy Constructor\n"; }

       SomeStruct(SomeStruct&& other) { std::cout << "In Move Constructor\n"; }

       SomeStruct& operator=(const SomeStruct& other)
       {
         std::cout << "In Copy Assignment Operator\n";
         return *this;
       }

       SomeStruct& operator=(SomeStruct&& other)
       {
         std::cout << "In Move Assignment Operator\n";
         return *this;
       }
     };

     int
     main()
     {
       SomeStruct a;
       SomeStruct b = SomeStruct();
       a = b;
       SomeStruct c(a);
       b = reinterpret_cast<SomeStruct&&>(c);
     }

  * Default constructor
  * Default constructor - *WHY NOT THE MOVE CONSTRUCTOR?*
  * Copy assignment operator
  * Copy constructor
  * Move assignment operator



* Tremend

  .. code-block:: c

     int
     square(volatile int* val)
     {
       return *val * *val;
     }

  * In case of an interrupt happening while dereferencing ``*val`` and
    modifying its value we could be using two different values.

    * dereference only once in a temporary and square that.

    * alter the prototype to accept a value, not a pointer.


  .. code-block:: cpp

     class A
     {
     public:
         void f() { cout << "Text\n"; }
         void g() { cout << x << "\n"; }
         int x;
     };

     int
     main()
     {
         A *a;
         a->f();
         a->g();
     }

  * Undefined behaviour because ``a`` is used uninitialized

  * Although ``f()`` will work because it's not accesing any class members

  * Method ``g()`` will either print garbage or segfault



  .. code-block:: c

     uint8_t
     f(uint8_t val)
     {
       if (val >= 128) {
         val - 128;
       } else {
         return val;
       }
     }

  * We can optimize it by doing ``val &= ~(1 << 7)``.



* Tellence

  .. code-block:: cpp

     #include "defs.h"
     #include <stdio.h>

     const char*
     inet_ntoa(uint32_t ip)
     {

       char buffer[IPV4_STR_SIZE];

       if (ip == 0) {
         return "0.0.0.0";
       }

       char* b = &ip;

       snprintf(buffer,
                sizeof(buffer),
                "%u %u %u %u",
                b[0],
                b[1],
                b[2],
                b[3]); // Endianness issues.

       return buffer; // Returning a buffer declared on the stack.
     }

  * What size does ``IPV4_STR_SIZE`` need to have?

    * 16

  * What does it mean for a variable to be declared ``static`` in a function?

    * Defined in the ``bss`` or ``data`` sections of the elf.
    * Only one instance of the buffer regardless of how many threads
      call the function.

  * If the buffer was declared `static` would the function be thread-safe?

    * No

  * What are the differences between ``static`` and ``global``?

    * ``static`` specifies that a variable is declared in ``.bss``, doesn't have
      automatic memory allocation, is only visible to that compilation unit i.e.
      internal linkage.

    * ``global`` variables reside in ``.data`` or ``.rodata`` sections,
      have ``static`` lifetime, are initialized to a value, can be accessed
      from other compilation units using ``extern`` i.e. external linkage.

  * If this function were to be called by a maximum of 10 threads how would
    you accomplish that?

    * Keep an array of 10 buffers, hash each thread upon function entry
      by thread id and use corresponding buffer.



Networking
==========

* How would a packet capture thread look like? (Tellence)

  * `Using epoll`_

* Routing tables (Tellence)

  * How does one look like?

    * Destination, Next Hop, Interface, Protocol, Metric, MetricType, State

  * What data structure would you use to implement one?

    * `Radix Trie`_, `Poptrie, DXR`_

      * `Routing DS Comparison`_, `Radix Trie Explanation`_,
        `Linux RIB`_, `FreeBSD RIB`_

* What is load-balancing? (Tellence)

  * Is a technique of distributing workload or information accross multiple
    links or to multiple processing units.
  * What is ECMP?

    * Equal Cost Multi Path is a routing technique used to forward packets
      to the same destination using multiple routes of same metric.
  * How would you implement a hashing algorithm for ECMP?

    * ``(src_mac ^ dst_mac + src_ip ^ dst_ip) % no_of_routes``

* OSPF_ (Tellence)

  * Link state routing protocol, Djikstra algorithm, uses IP
  * Uses areas for hierarchical routing, Seven LSA types for IPv4,
    Nine LSAs for IPV6


.. _Radix Trie: https://en.wikipedia.org/wiki/Radix_tree
.. _Poptrie, DXR: http://conferences.sigcomm.org/sigcomm/2015/pdf/papers/p57.pdf
.. _Routing DS Comparison: https://pdfs.semanticscholar.org/563f/f3059cf0d0bf7d9bef0c0d17c890e47f5090.pdf
.. _Radix Trie Explanation: https://trie.now.sh
.. _Linux RIB: https://vincent.bernat.ch/en/blog/2017-ipv4-route-lookup-linux
.. _FreeBSD RIB: https://www.openbsd.org/papers/eurobsdcon2016-embracingbsdrt.pdf
.. _Using epoll: https://bitbucket.org/rhadamanthus/vulny/src/e7bfc11780030d736ac2d7c766a50d7b4fdd9f6d/lib/async_io.cc#lines-138
.. _OSPF: https://www.inetdaemon.com/tutorials/internet/ip/routing/ospf
