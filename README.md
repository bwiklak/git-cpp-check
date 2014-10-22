git-cpp-check
=============

Combines [git-clang-format](https://llvm.org/svn/llvm-project/cfe/trunk/tools/clang-format/git-clang-format) (found i.e. in [LLVM package](http://llvm.org/releases/download.html)) and [cpplint](http://google-styleguide.googlecode.com/svn/trunk/cpplint/cpplint.py) to check you code before commit.

Usage
-----
1. Copy git-cpp-check, [git-clang-format](https://llvm.org/svn/llvm-project/cfe/trunk/tools/clang-format/git-clang-format) and [cpplint](http://google-styleguide.googlecode.com/svn/trunk/cpplint/cpplint.py) to a single directory (or make links).
2. Add clang-format tool to your system path.
3. Make git-cpp-check executable.
4. Add git-cpp-check to your pre-commit script.

How does it work?
-----------------
git-cpp-check works on files added to you git commit. Cpplint reads the whole file but only lines changed by commit are taken under account. This means that if there are i.e. linter errors in other parts of your code untouched by commit no error will be reported[1].

git-clang-format runs first, it there are some changes, diff is displayed and commit is stopped. Changes are staged, if you accept them you can simply run `commit -a` again. 
When no formatting errors are found cpplit is used.

Notes
-----
If you don't want the script to be run, add `-n` option to the `commit` command.

Limitations
-----------
Current version of git-cpp-check takes no parameters, you cannot fine tune your linter and formatter as you could using these tools separately. Clang format uses Chromium style.


---
[1] This could be improved by displaying a warning.
