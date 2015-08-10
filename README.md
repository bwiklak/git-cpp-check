git-cpp-check
=============

Combines [git-clang-format](https://llvm.org/svn/llvm-project/cfe/trunk/tools/clang-format/git-clang-format) (found e.g. in [LLVM package](http://llvm.org/releases/download.html)) and [cpplint](http://google-styleguide.googlecode.com/svn/trunk/cpplint/cpplint.py) to check your code before commit.

Usage
-----

1. Get:
   - git-cpp-check,
   - [git-clang-format](https://llvm.org/svn/llvm-project/cfe/trunk/tools/clang-format/git-clang-format),
   - [cpplint](http://google-styleguide.googlecode.com/svn/trunk/cpplint/cpplint.py),
   - [clang-format](http://llvm.org/builds/).
2. Add clang-format tool to your system path *or* tell git where it is:
   `$ git config clangformat.binary path/to/clang-format[.exe]`
3. Make `git-cpp-check` executable.
4. Add `git-cpp-check` to your pre-commit script. If `git-clang-format` or `cpplint.py` aren't in the same directory as `git-cpp-check`, you need to tell `git-cpp-check` where they are. Edit `.git/hooks/pre-commit`:
  `/path/to/git-cpp-check --cpplint="/path/to/cpplint.py" --git-clang-format="/path/to/git-clang-format"`
  The paths can be relative to the git root directory.

How does it work?
-----------------
Git-cpp-check works on files added to you git commit. Cpplint reads the whole file but only lines changed by commit are taken into account. This means that if there are e.g. Cpplint errors in other parts of your code untouched by commit, no error will be reported [1].

Working with git-cpp-check
--------------------------
When working with git-cpp-check, you want to stage (`git add`) all your changes before attempting to commit them. The script will refuse to modify any file with unstaged changes.

Once you've staged all your changes, commit them like usually - the pre-commit hook will be executed. This can lead to:

1. The commit proceeds as usual, which means your changes were simply accepted.
2. The script reported cpplint issues, listing them for you. The commit is automatically aborted. You need to fix the issues, stage them (`git add`) and try to commit again.
3. The script modified some files with clang-format. The changes are listed for you in the diff format. At this point the command `git add -p` can be recommended. Git will ask you about each change whether you want to stage it or not. Usually you'll accept all the changes, but sometimes you can manually format your code better than clang-format or you just don't want to reformat a large multi-line expression just because you changed one word. If you want to reject any change, just don't stage it - answer "n" and leave it unstaged. Then just try to commit again. The script won't complain about the unstaged changes, because it won't have any additional changes to apply, and only the staged changes will be committed. After the commit succeeds, you can get rid of the unstaged clang-format changes by running `git reset --hard`.

Notes
-----
If you don't want the script to be run, add `-n` option to the `commit` command.

Limitations
-----------
Current version of git-cpp-check doesn't allow you to fine tune your linter and formatter as you could using these tools separately. Clang format uses Chromium style.

---
[1] This could be improved by displaying a warning.
