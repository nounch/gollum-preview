# Gollum-preview

## Description

gollum-preview lets you preview file and directory changes in a [Gollum](https://github.com/gollum/gollum/) repository without having to commit each change for Gollum to actually render it. Just run `./gollum-preview`, edit your markup files and instantly reload your Wiki pages in the browser to preview. Then simply commit your changes.

*Problem 1:* Gollum forces you to use its web interface to edit files if you want instant preview or, if you rebel use your favorite text editor instead, to do a commit each time before being able to refresh the matching page in the browser. This is not convenient.

*Solution:* Gollum-preview makes it possible to edit Gollum Wikis with your favorite text editor while still having Gollum render the individual pages for preview in the browser as soon as you refresh a page.

*Problem 2:* When using the Gollum web interface you can only edit one page at a time per commit. If you use your text editor instead, you cannot preview your changes until you have commited them. If you made some mistakes, you have to revert your last commit manually.

*Solution:* The gollum-preview script lets you group logically coherent changes in different files and directories in one single commit without having to revert commits yourself.

## How does it work?

By default, gollum-preview runs an infinite loop which adds all changes to `HEAD` every second and commits them using Git's `--amend` option. So `HEAD` will be replaced over and over again without creating an incredible number of commits. As soon as the script is aborted using `Ctrl-c`, this top-most `HEAD` commit will be reset so that only your previous commit history will be restored. Will this not impact my last proper commit? No. Gollum-preview does a dummy commit before applying its `--amend` commits.

The script also supports reloading only on file changes. To do so, uncomment the `while true` line in the script and add your favorite file changes monitoring tool instead and tell it to run `git add -A && git commit -m '...' --amend` on file changes.

*Note on inotifywait*: Since inotifywait is
[not able](http://superuser.com/questions/181517/how-to-execute-a-command-whenever-a-file-changes)
to handle modifications to the files it is watching caused by the very code it is executing on file changes, this may lead to problems if you have code-modifying pre/post/...-commit hooks in place (e.g. code linters). `fsmonitor` is a sensible alternative. gollum-preview comes with an example line (commented out by default) showing how to use it.

## Usage

1. Drop the `gollum-preview` script into the root of your Gollum Wiki.
2. Make it executable (`chmod +x gollum-preview`).
3. Run it: `./gollum-preview [PORT]` (It automatically runs the Gollum server for you; the port is optional.)
4. Edit away *- Now you can preview your changes instantaneously in the browser!*
5. Hit `Ctrl-c` to stop the script (Important! Otherwise you have to run `git reset HEAD~1` manually)
6. Commit your changes

If you want to use `fsmonitor` to only do commits on file changes, run...

    npm install -g fsmonitor

..., comment out the `while true` line and uncomment the `fsmonitor` line instead.

If you wish to use a different Gollum configuration, replace the invocation of `gollum` in `gollum-preview` with your desired parameters.
