# Beginner projects for terminal, Git, GitHub, and tmux

These projects help build the Phase 2 foundation in rwxrob's [Beginner Boost track](https://github.com/rwxrob/boost/blob/main/docs/beginner/README.md): Terminal, Git, GitHub, and Copilot. They are designed to build on each other, with tmux as a personal area of interest.

The [mandatory-command list](https://github.com/rwxrob/boost/blob/main/docs/beginner/mandatory-commands.md) is a reference, not a test to pass all at once. Practice relevant commands as they come up, learn what they do, and return to the broader list over time. Some commands differ by operating system. Commands that can change system settings or delete data, such as `rm`, `chmod`, `chown`, and `sudo`, should wait until you understand their effects and have a safe place to practice.

## Project 1: Terminal command field guide

Create a small GitHub repository with a README and beginner-friendly examples of commands you have tried. For each command, record what it does, a safe example, and what you observed.

Start with `pwd`, `ls`, `cd`, `mkdir`, `touch`, `cat`, `less`, `cp`, `mv`, `find`, `grep`, `head`, `tail`, and `diff`. As you need them, try `tree`, `file`, `sort`, `wc`, `echo`, and `date`. Use sample files in your project folder so you can explore without changing important data.

Track small improvements with GitHub issues. Before each commit, check `git status`, inspect your changes with `git diff`, stage the files you intend to save with `git add`, and make a focused commit with `git commit`. Push your commits with `git push`.

**Checkpoint:** A beginner can follow several examples and describe what happened in the terminal.

## Project 2: tmux study workspace

Add a guide to your own tmux practice setup. In a terminal, create a named session, open another window or split the current window, and practice detaching from and reattaching to the session. Useful commands to explore include `tmux new -s study`, `tmux ls`, `tmux attach -t study`, and `tmux split-window`.

Record what each command does, how you can tell which session is active, and how to end the practice session. Keep the setup project-specific for now; avoid changing your global terminal configuration until you know what you want.

Use Copilot as a tutor: ask it to explain one command or propose a small improvement to your guide. Read any suggested command before running it, then check the result yourself.

**Checkpoint:** You can leave a tmux session and return to it, and your guide explains the steps in your own words.

## Project 3: Tech-role research notebook

Build a Markdown notebook comparing two or three technology roles that interest you. Link to real job descriptions, note skills that recur, and keep a short list of questions to investigate. Verify claims against the job descriptions and conversations with people in the field.

Use terminal commands such as `grep`, `sort`, `head`, and `column -t` to find or organize information in your own notes. Ask Copilot to explain unfamiliar terms or suggest a useful way to structure the comparison, then verify the information and edit the notes yourself.

**Checkpoint:** You can describe which role you want to explore next and what evidence informed your choice.

## Project 4: Learning portfolio

Turn your notes and project work into a simple Markdown or HTML site. If you are ready, publish it with GitHub Pages. Practice checking links, reading the published page, and diagnosing one issue.

Try a separate Git branch for a change you want to experiment with. Use `git status` and `git diff` to review your work before committing and pushing it.

**Checkpoint:** Someone else can view the project, understand what you learned, and follow its instructions.

## Project 5: Terminal learning lab capstone

Combine the most useful parts into a small terminal learning lab: a command guide, a tmux quick start, a learning journal, and links to your role research. Use one GitHub issue or task per small improvement, commit focused changes, and ask Copilot for help one question or change at a time.

Review Copilot's suggestions before accepting them. Understand commands before running them, inspect the diff, and try the instructions yourself from a clean terminal session.

**Checkpoint:** Another beginner can follow the guide to complete a basic terminal exercise and start a tmux session.

## Repeat this Git routine

Use this sequence whenever you make a change:

1. Run `git status` to see what changed.
2. Make one small improvement.
3. Run `git diff` to review it.
4. Run `git add <file>` for the file you intend to commit.
5. Run `git status` again to check what is staged.
6. Run `git commit -m "Describe the change"` to save a focused snapshot.
7. Run `git push` to publish your commit to GitHub.

As you practice, learn what the working tree, staged changes, commit, branch, and remote mean. The [Git basics quick reference](https://github.com/rwxrob/boost/blob/main/docs/beginner/git-basics-notes.md) is a companion.

## Suggested progression

Start with the command field guide, add the tmux workspace as its first substantial section, then grow the repository into the capstone. Add the research notebook and portfolio when they support your interests. A small, finished project that you can explain is more valuable than trying to learn every command or tool at once.
