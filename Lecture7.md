# Lecture7: Debugging and Profiling
  
## Debugging
  
1. **Use `journalctl` on Linux or `log show` on macOS to get the super user accesses and commands in the last day. If there aren't any you can execute some harmless commands such as `sudo ls` and check again.**
    
    Solution:
    ```bash
    journalctl -S yesterday -U today
    ```
    
    ---
2. **Do [this](https://github.com/spiside/pdb-tutorial) hands on `pdb` tutorial to familiarize yourself with the commands. For a more in depth tutorial read [this](https://realpython.com/python-debugging-pdb).**
    
    (Omitted)
    
    ---
3. **Install [`shellcheck`](https://www.shellcheck.net/) and try checking the following script. What is wrong with the code? Fix it. Install a linter plugin in your editor so you can get your warnings automatically.**
    ```bash
    #!/bin/sh
    ## Example: a typical script with several problems
    for f in $(ls *.m3u)
    do
     grep -qi hq.*mp3 $f \
       && echo -e 'Playlist $f contains a HQ file in mp3 format'
    done
    ```
    
    I've installed [ale](https://vimawesome.com/plugin/ale) for automatically checking. Some versions of `vim` would cause the [cursor being invisble](https://github.com/dense-analysis/ale/issues/1536) while navigating error/warning lines provided by ale and it's a bug of `vim`. I solved this by [updating `vim` by PPA](https://vi.stackexchange.com/questions/10817/how-can-i-get-a-newer-version-of-vim-on-ubuntu/10827#10827).
    
    Script after debugging:
    ```bash
    #!/bin/sh
    ## Example: a typical script with several problems
    for f in *.m3u
    do
      grep -qi "hq.*mp3 $f" \
        && echo -e "Playlist $f contains a HQ file in mp3 format"
    done
    ```
    
    ---
4. **(Advanced) Read about [reversible debugging](https://undo.io/resources/reverse-debugging-whitepaper/) and get a simple example working using [`rr`](https://rr-project.org/) or [`RevPDB`](https://morepypy.blogspot.com/2016/07/reverse-debugging-for-python.html).**
    
    (Omitted)
    
---
## Profiling
    
5. **[Here](/static/files/sorts.py) are some sorting algorithm implementations. Use [`cProfile`](https://docs.python.org/3/library/profile.html) and [`line_profiler`](https://github.com/pyutils/line_profiler) to compare the runtime of insertion sort and quicksort. What is the bottleneck of each algorithm? Use then `memory_profiler` to check the memory consumption, why is insertion sort better? Check now the inplace version of quicksort. Challenge: Use `perf` to look at the cycle counts and cache hits and misses of each algorithm.**

    Insert `@profile` above the definition of each target algorithm and then use line_profiler `kernprof` to show the time taken:
    ```bash
    kernprof -l -v sorts.py
    ```
    Lines that occupied the largest percentage would be the bottleneck of that algorithm. 
    
    Type following command to use memory_profiler:
    ```bash
    python -m memory_profiler example.py
    ```
    `insertionsort`'s memory consumption is less than `quicksort` since the later need additional space for temporary arrays(`left` and `right`) in every recursion level. 
    
    (Challenge part is omitted)
    
    ---
6. **Here's some (arguably convoluted) Python code for computing Fibonacci numbers using a function for each number.**
    ```python
    #!/usr/bin/env python
    def fib0(): return 0

    def fib1(): return 1

    s = """def fib{}(): return fib{}() + fib{}()"""

    if __name__ == '__main__':

       for n in range(2, 10):
           exec(s.format(n, n-1, n-2))
       # from functools import lru_cache
       # for n in range(10):
       #     exec("fib{} = lru_cache(1)(fib{})".format(n, n))
       print(eval("fib9()"))
    ```
    **Put the code into a file and make it executable. Install prerequisites: [`pycallgraph`](http://pycallgraph.slowchop.com/en/master/) and [`graphviz`](http://graphviz.org/). (If you can run `dot`, you already have GraphViz.) Run the code as is with `pycallgraph graphviz -- ./fib.py` and check the `pycallgraph.png` file. How many times is `fib0` called?. We can do better than that by memoizing the functions. Uncomment the commented lines and regenerate the images. How many times are we calling each `fibN` function now?**
    
    Use `xdg-open pycallgraph.png` command to open the picture and can see that `fib0` was called 21 times.

    After uncommenting, there is an error `ImportError: cannot import name lru_cache` and it can be solved by modifying the first commented line into `from backports.functools_lru_cache import lru_cache`([Source](https://pypi.org/project/backports.functools-lru-cache/)). After that , the graph would be successfully generated and both `fib0` and `fib1` were called 1 time now.
    
    ---
7. **A common issue is that a port you want to listen on is already taken by another process. Let's learn how to discover that process pid. First execute `python -m http.server 4444` to start a minimal web server listening on port `4444`. On a separate terminal run `lsof | grep LISTEN` to print all listening processes and ports. Find that process pid and terminate it by running `kill <PID>`.**
    
    `python -m http.server 4444` is not working for me(no any reaction for unknown reason) and I have to use `python3` instead.
    
    ---
8. **Limiting processes resources can be another handy tool in your toolbox. Try running `stress -c 3` and visualize the CPU consumption with `htop`. Now, execute `taskset --cpu-list 0,2 stress -c 3` and visualize it. Is `stress` taking three CPUs? Why not? Read [`man taskset`](https://www.man7.org/linux/man-pages/man1/taskset.1.html). Challenge: achieve the same using [`cgroups`](https://www.man7.org/linux/man-pages/man7/cgroups.7.html). Try limiting the memory consumption of `stress -m`.**
        
    `stress -c 3` uses up 3 CPU for each `stress` command.\
    `taskset --cpu-list 0,2 stress -c 3` takes only 1 CPU  for each stress command.
    
    Explanation from `man taskset`:
    > The Linux scheduler will honor the given CPU affinity and the process will not run on any other CPUs.
    
    (Challenge part is omitted)
    
    ---
9. **(Advanced) The command `curl ipinfo.io` performs a HTTP request and fetches information about your public IP. Open [Wireshark](https://www.wireshark.org/) and try to sniff the request and reply packets that `curl` sent and received. (Hint: Use the `http` filter to just watch HTTP packets).**
    
    (Omitted)
    
