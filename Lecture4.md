# Lecture 4 : Data Wrangling
  
1. **Take this [short interactive regex tutorial](https://regexone.com/).**
  
    (Omitted)
  
    ---
2. **Find the number of words (in `/usr/share/dict/words`) that contain at least three `a`s and don't have a `'s` ending. What are the three most common last two letters of those words? `sed`'s `y` command, or the `tr` program, may help you with case insensitivity. How many of those two-letter combinations are there? And for a challenge: which combinations do not occur?**
  
    Solution:
    ```bash
    # find the number of words (in /usr/share/dict/words) that contain at least three as and don’t have a 's ending
    cat /usr/share/dict/words | awk '$1 ~ /.*a.*a.*a.*[^(\Ws)]$/ { print $1 }' | wc -l

    # what are the three most common last two letters of those words?
    cat /usr/share/dict/words | awk '$1 ~ /.*a.*a.*a.*[^(\Ws)]$/ { print $1 }' | sed -E 's/.*(..)/\1/' | sort | uniq -c | sort -n -k1,1 | tail -n 3 | awk '{ print $2 }'

    # how many of those two-letter combinations are there?
    cat /usr/share/dict/words | awk '$1 ~ /.*a.*a.*a.*[^(\Ws)]$/ { print $1 }' | sed -E 's/.*(..)/\1/' | sort | uniq -c | wc -l

    # which combinations do not occur?
    cat /usr/share/dict/words | awk '$1 ~ /.*a.*a.*a.*[^(\Ws)]$/ { print $1 }' | sed -E 's/.*(..)/\1/' | sort | uniq -c | awk '{ print $2 }' > exist_letter.txt
    awk 'BEGIN { for(i=97;i<=122;i++)for(j=97;j<=122;j++) printf "%c%c\n",i,j}' > all_letter.txt
    diff --changed-group-format='%<' --unchanged-group-format='' allletter.txt existletter.txt
    ```
  
    ---
3. **To do in-place substitution it is quite tempting to do something like `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt`. However this is a bad idea, why? Is this particular to `sed`? Use `man sed` to find out how to accomplish this.**
  
    Solution:
    ```bash
    sed -i s/REGEX/SUBSTITUTION/ input.txt
    ```
    Reference from [https://github.com/Ivan-Kim/MIT-missing-semester/tree/master/Lecture4](https://github.com/Ivan-Kim/MIT-missing-semester/tree/master/Lecture4) :
    > `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt` will return a blank file, since `input.txt` on the right hand side of the output operator `>` will be made blank before the left hand side of the output operator could be applied. Because the file names conicide, an empty `input.txt` will be output to an empty `input.txt`.\
    It is generally a bad idea to in-place edit files as mistakes can be made when using regular expressions and without backups on the original file, it can be very difficult to recover. This is not particular to sed but in general to all forms of in-place editing files. To accomplish in-place substitution add the `-i` tag.
  
    ---
4. ***Find your average, median, and max system boot time over the last ten boots. Use `journalctl` on Linux and `log show` on macOS, and look for log timestamps near the beginning and end of each boot. On Linux, they may look something like:***
    ```
    Logs begin at ...
    ```
    ***and***
    ```
    systemd[577]: Startup finished in ...
    ```
    ***On macOS, [look for](https://eclecticlight.co/2018/03/21/macos-unified-log-3-finding-your-way/):***
    ```
    === system boot:
    ```
    ***and***
    ```
    Previous shutdown cause: 5
    ```
    
    Solution:
    ```bash
    # list the system boot time over the last ten boots
    # but I don't know how to deal with the different units in order to calculate avarage/median/max
    journalctl | grep 'systemd\[1\]: Startup finished in' | tail -n10 | sed -E 's/.*= ([0-9\.]+.*)\.$/\1/'
    ```
    
    ---
5. ***Look for boot messages that are _not_ shared between your past three reboots (see `journalctl`'s `-b` flag). Break this task down into multiple steps. First, find a way to get just the logs from the past three boots. There may be an applicable flag on the tool you use to extract the boot logs, or you can use `sed '0,/STRING/d'` to remove all lines previous to one that matches `STRING`. Next, remove any parts of the line that _always_ varies (like the timestamp). Then, de-duplicate the input lines and keep a count of each one (`uniq` is your friend). And finally, eliminate any line whose count is 3 (since it _was_ shared among all the boots).***
    
    Solution:
    ```bash
    # there seems to be sth wrong with the logs getting by 'journalctl' command as some message appear more than 3 three
    journalctl -b -2 | tail -n +2  | sed -E 's/.*([0-9]]:|kernel:) (.*)$/\2/' | sort | uniq -c | awk ' $1 != 3 { print }'
    ```
    
    ---
6. **Find an online data set like [this one](https://stats.wikimedia.org/EN/TablesWikipediaZZ.htm), [this one](https://ucr.fbi.gov/crime-in-the-u.s/2016/crime-in-the-u.s.-2016/topic-pages/tables/table-1), or maybe one [from here](https://www.springboard.com/blog/free-public-data-sets-data-science-project/). Fetch it using `curl` and extract out just two columns of numerical data. If you're fetching HTML data, [`pup`](https://github.com/EricChiang/pup) might be helpful. For JSON data, try [`jq`](https://stedolan.github.io/jq/). Find the min and max of one column in a single command, and the difference of the sum of each column in another.**
    
    Solution:
    ```bash
    # find the max of one column in a single command
    curl https://ucr.fbi.gov/crime-in-the-u.s/2016/crime-in-the-u.s.-2016/topic-pages/tables/table-1 | pup 'td[class="odd group1 alignright valignmentbottom numbercell"]' | sed -E 's/^<.*>$//' | sort | sed -E 's/ ([0-9]+),([0-9]+),([0-9]+)/\1\2\3/' | grep -v '^$' | tail -n 1

    # calculate the sum of one column
    curl https://ucr.fbi.gov/crime-in-the-u.s/2016/crime-in-the-u.s.-2016/topic-pages/tables/table-1 | pup 'td[class="odd group1 alignright valignmentbottom numbercell"]' | sed -E 's/^<.*>$//' | sort | sed -E 's/ ([0-9]+),([0-9]+),([0-9]+)/\1\2\3/' | grep -v '^$' | paste -sd+ | bc
    ```
    
