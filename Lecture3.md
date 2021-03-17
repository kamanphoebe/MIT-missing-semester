# Lecture 3 : Editors (Vim)
  
1. **Complete `vimtutor`. Note: it looks best in a [80x24](https://en.wikipedia.org/wiki/VT100) (80 columns by 24 lines) terminal window.**
  
	(Omitted)
  
    ---
2. **Download our [basic vimrc](/2020/files/vimrc) and save it to `~/.vimrc`. Read through the well-commented file (using Vim!), and observe how Vim looks and behaves slightly differently with the new config.**
     
	(Omitted)
  
    ---
3. **Install and configure a plugin: [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim).**
    1. **Create the plugins directory with `mkdir -p ~/.vim/pack/vendor/start`**
    1. **Download the plugin: `cd ~/.vim/pack/vendor/start; git clone https://github.com/ctrlpvim/ctrlp.vim`**
    1. **Read the [documentation](https://github.com/ctrlpvim/ctrlp.vim/blob/master/readme.md) for the plugin. Try using CtrlP to locate a file by navigating to a project directory, opening Vim, and using the Vim command-line to start `:CtrlP`.**
    1. **Customize CtrlP by adding [configuration](https://github.com/ctrlpvim/ctrlp.vim/blob/master/readme.md#basic-options) to your `~/.vimrc` to open CtrlP by pressing Ctrl-P.**
     
	(Omitted)
  
    ---
4. **To practice using Vim, re-do the Demo from lecture on your own machine.**
  
    Solution:
    ```python
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
  
    ---
5. **Use Vim for _all_ your text editing for the next month. Whenever something seems inefficient, or when you think "there must be a better way", try Googling it, there probably is. If you get stuck, come to office hours or send us an email.**
     
	(Omitted)
  
    ---
6. **Configure your other tools to use Vim bindings (see instructions above).**
     
	(Omitted)
  
    ---
7. **Further customize your `~/.vimrc` and install more plugins.**
     
	(Omitted)
  
    ---
8. **(Advanced) Convert XML to JSON ([example file](/2020/files/example-data.xml)) using Vim macros. Try to do this on your own, but you can look at the [macros](#macros) section above if you get stuck.**
     
	(Omitted)
  
