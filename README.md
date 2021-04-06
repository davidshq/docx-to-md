# DOCX-TO-MD

## What Does It Do?

These shell scripts create a markdown copy of a Microsoft Word Document (.docx). This is facilitate diffing of commits since .docx is a binary format and git's built-in capabilities are not fully satisfactory.

## Did You Write This?

The scripts as initially committed to this repository were part of the [vigente/gerardus repo](https://github.com/vigente/gerardus/).

You can find the script files in the original repo below:
- [post-commit-git-diff-docx.sh](https://github.com/vigente/gerardus/blob/master/shell-script/post-commit-git-diff-docx.sh)
- [pre-commit-git-diff-docx.sh](https://github.com/vigente/gerardus/blob/master/shell-script/pre-commit-git-diff-docx.sh)

This repo also uses the wiki article from [vigente/gerardus, "Integrate git diffs with word docx files."](https://github.com/vigente/gerardus/wiki/Integrate-git-diffs-with-word-docx-files)

## Why Would You Do This?

Initially I had thought to fork vigente/gerardus but it is quite a large repo and I am only interested in these particular scripts.

## How To: See diffs of .docx files with git `wdiff {file}.docx`
This section was inspired by [Martin Fenner's "Using Microsoft Word with git"](https://sensiblescience.io/mfenner/using-microsoft-word-with-git).

To configure git diff:

1. Install pandoc.
2. Tell git how to handle diffs of .docx files.
    Create or edit file `~/.gitconfig` (Linux, Mac) or `"C:\Documents and Settings\user.gitconfig"` (Windows) and add:

```
[diff "pandoc"]
    textconv=pandoc --to=markdown
    prompt = false
[alias]
    wdiff = diff --word-diff=color --unified=1
```

3. In your document directory, create or edit the file `.gitattributes` (Linux, Windows and Mac) and add: `*.docx diff=pandoc`

    You can commit .gitattributes so that it stays with your paper for use in other computers, but you'll need to edit ~/.gitconfig in every new computer you want to use.
Now you can see a pretty colored diff with the changes you have made to your .docx file since the last commit.

        `git wdiff file.docx`

      To see all changes over time:

        `git log -p --word-diff=color file.docx`

## Track changes in Word (.docx) documents by committing a .md file with changes
### Automatically when running git commit.
This is only going to work from Linux/Mac or Windows running git from a bash shell.

1. Install pandoc. Pandoc is a program to convert between different file formats. It's going to allow us to convert Word files (.docx) to Markdown (.md).
2. Set up git hooks to enable automatic generation and tracking of Markdown copies of .docx files.
    Copy these hook files to your git project's .git/hooks directory and rename them, or soft-link to them with ln -s, and make them executable (chmod u+x *.sh):

        [pre-commit-git-diff-docx.sh](pre-commit-git-diff.docx.sh) -> .git/hooks/pre-commit
        [post-commit-git-diff-docx.sh](post-commit-git-diff-docx.sh) -> .git/hooks/post-commit

    Now every time you run git commit, the pre-commit hook will automatically run before you see the window to enter the log message. The hook is a script that makes a copy in Markdown format (.md) of every .docx file you are committing. The post-commit hook then amends the commit adding the .md files.

### Manually by creating a Markdown copy of the .docx file.
This works on Linux, Mac and Windows.

1. Install pandoc.
2. Edit your Word document as needed.
3. Run pandoc from the command line. This will create a Markdown version of your file (without Figures, but with equations in latex format): `pandoc -s file.docx -t markdown -o file.md`
4. Update the ChangeLog
5. Commit both files with git
    ```
    git add file.docx file.md
    git commit
    ```

### Additional Notes
- You can also add a `.gitconfig` file on a per repo level so you don't have to edit the `.gitconfig` on every new computer.