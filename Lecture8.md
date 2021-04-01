# Lecture8: Metaprogramming
  
1. **Most makefiles provide a target called `clean`. This isn't intended to produce a file called `clean`, but instead to clean up any files that can be re-built by make. Think of it as a way to "undo" all of the build steps. Implement a `clean` target for the `paper.pdf` `Makefile` above. You will have to make the target [phony](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html). You may find the [`git ls-files`](https://git-scm.com/docs/git-ls-files) subcommand useful. A number of other very common make targets are listed [here](https://www.gnu.org/software/make/manual/html_node/Standard-Targets.html#Standard-Targets).**
    
    Solution:
    ```makefile
    paper.pdf: paper.tex plot-data.png
	pdflatex paper.tex

    plot-%.png: %.dat plot.py
        ./plot.py -i $*.dat -o $@

    .PHONY: clean
    clean:
        rm paper.pdf
        rm paper.log
        rm paper.aux
        rm plot-data.png
    ```
        
    ---
2. **Take a look at the various ways to specify version requirements for dependencies in [Rust's build system](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html). Most package repositories support similar syntax. For each one (caret, tilde, wildcard, comparison, and multiple), try to come up with a use-case in which that particular kind of requirement makes sense.**
    
    - **Caret**  
    If your program depends on an API which only exists from version `1.2.3` to `1.9`, then version requirment would be `^1.2.3`
    - **Tilde**  
    If your program depends on an API which only exists from version `1.0` to `1.9`, then version requirment would be `~1`
    - **Wildcard**  
    If your program does not matter of different versions of library, then version 
                      requirment would be `*`
    - **Comparsion**  
    If your program depends on an API which has been deleted since version `1.2.0`, then version requirment would be `< 1.2.0` 
    - **Multiple**  
    If your program depends on an API which only exists from version `1.2` to `1.5`, then version requirment would be `>= 1.2`, `< 1.6`
    
    ---
3. **Git can act as a simple CI system all by itself. In `.git/hooks` inside any git repository, you will find (currently inactive) files that are run as scripts when a particular action happens. Write a [`pre-commit`](https://git-scm.com/docs/githooks#_pre_commit) hook that runs `make paper.pdf` and refuses the commit if the `make` command fails. This should prevent any commit from having an unbuildable version of the paper.**
    
    Create and edit `.git/hooks/pre-commit`:
    ```bash
    #!/bin/sh
    if ! make paper.pdf
    then
        exit 1
    fi
    ```
    
    ---
4. **Set up a simple auto-published page using [GitHub Pages](https://pages.github.com/). Add a [GitHub Action](https://github.com/features/actions) to the repository to run `shellcheck` on any shell files in that repository (here is [one way to do it](https://github.com/marketplace/actions/shellcheck)). Check that it works!**
    
    I looked up the [official website of GitHub Pages](https://pages.github.com/) and [its documents](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions) for help. First of all, create a repository called `{{username}}.github.io` in Github and clone it to local. Then create a new `.yml` file (content below) in `{{username}}.github.io/.github/workflows/` directory, commit the changes and finally push to Github.
    ```
    on:
      push:
        branch:
          - master

    jobs:
      shellcheck:
        name: Shellcheck
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Run ShellCheck
          uses: ludeeus/action-shellcheck@master
    ```
    
    ---
5. ***[Build your own](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/building-actions) GitHub action to run [`proselint`](http://proselint.com/) or [`write-good`](https://github.com/btford/write-good) on all the `.md` files in the repository. Enable it in your repository, and check that it works by filing a pull request with a typo in it.***
    
    The steps are almost the same as those in ex.4. Create a repository called `{{username}}.github.io` in Github and clone it to local. Then create a new `.yml` file (content below) in `{{username}}.github.io/.github/workflows/` directory, commit the changes and finally push to Github.\
    However, things do not work properly. Although `proselint` runs whenever there is a pull request, it doesn't detect any typo.
    ```
    on:
      push:
        branch:
          - master

    jobs:
      proselint:
        name: Proselint
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          - name: Run Proselint
            uses: dhruvmanila/action-proselint@master
    ```
    
