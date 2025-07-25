+++
title = "Vim Find and Replace"
author = ["Kiran"]
date = 2024-05-30T20:45:00-04:00
tags = ["vim"]
draft = false
css = "../../zcustom.css"
+++

[Reference](https://linuxize.com/post/vim-find-replace/)


## Find and Replace Command {#find-and-replace-command}

The command to find and replace a word in a document is by the use of :subtitute (:S) command. The basic command is as follows:

```nil
:[range]s/{pattern}/{string}/[flags] [count]
```

-   [range] = the word to be replaced will be searched within this specified lines.
-   {pattern} = the word that has to be replaced
-   {string} = the new replaced word
-   [flags] = is constraint for the command
-   [count] = is a postive number that mutliplies the command

Note: To replace a special charater use "\\" before the character, for example, to replace $apple =&gt; ^orange use "

```nil
:%s/\$apple/\^orange/g".
```

Examples:

1.  :s/foo/bar/g =&gt; replaces foo with bar in the current line.
2.  :%s/foo/bar/g =&gt; replaces foo with bar in the entire document.

Some necessary flags:

1.  g =&gt; is used to replaces the occurances.
2.  c =&gt; is used to confirm the replacement.
3.  i =&gt; is used to ignore case sentivity.
