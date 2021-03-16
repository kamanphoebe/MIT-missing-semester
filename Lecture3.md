# Lecture 3 : Editors (Vim)
---

**4. To practice using Vim, re-do the Demo from lecture on your own machine.**
```
import sys

def fizz_buzz(limit):
    for i in range(1, limit):
        if i % 15 == 0:
            print('fizzbuzz')
            continue
        if i % 3 == 0:
            print('fizz')
        if i % 5 == 0:
            print('buzz')
        if i % 3 and i % 5:
            print(i)

def main(argv):
    fizz_buzz(int(argv[1]))

if __name__ == '__main__':
	main(sys.argv)
```
