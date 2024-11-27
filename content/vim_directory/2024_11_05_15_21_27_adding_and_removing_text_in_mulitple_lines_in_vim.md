+++
title = "Adding and Removing Text in mulitple lines in Vim"
author = ["Kiran"]
date = 2024-11-05T15:21:00-05:00
tags = ["vim"]
draft = false
css = "../../zcustom.css"
+++

## Adding Text at the Beginning of Multiple Lines {#adding-text-at-the-beginning-of-multiple-lines}

-   Go the line you want to select the text.
-   Press **V** (upppercase `V`) to state Visual line mode.
-   Select multiple line using arrow or j/k keys.
-   Press `:` after selecting the desired lines. This will open the line range command `:'<,'>`.
-   Type `s/^/<text>/`, where "&lt;text&gt;" is the text you want to add at the beginning of each fo the selected lines. `(^)` indicats the will be added to the beginning of the line.


#### Example 1: Add text to the selected lines {#example-1-add-text-to-the-selected-lines}

Adding comments `//` to each line in verilog.

```vim
:'<','>s/^/\/\/ /
```


#### Example 2: Add text to the entire files {#example-2-add-text-to-the-entire-files}

```vim
:%s/^/\/\/ /
```

-   Press `Esc` to navigate to normal mode.
-   `%` applies the command to all the lines in the file.


## Removing Text at the Beginning of Multiple Line {#removing-text-at-the-beginning-of-multiple-line}

-   Press `Esc` to navigate to normal mode.
-   Press **V** (upppercase `V`) to state Visual line mode.
-   Select multiple line using arrow or j/k keys.
-   Press `:` after selecting the desired lines. This will open the line range command `:'<,'>`.
-   Type `s/^..//`, where `(^)` indicates the **start** of the line and `(..)` will select excatly two characters and `(//)` will delete them.


#### Example 1: Delete the first two characters from the selected lines {#example-1-delete-the-first-two-characters-from-the-selected-lines}

Remove the comments `//` for the selected lines.

```vim
:<','>s/^..//
```


#### Example 2: Delete the first two character from all the lines in a file {#example-2-delete-the-first-two-character-from-all-the-lines-in-a-file}

```nil
:%s/^..//
```

-   Ensure you in the normal by pressing `Esc` before executing the above command.
-   `%` applies the command to all the line in the file.


## Adding Text to End of Multiple Line {#adding-text-to-end-of-multiple-line}

-   Go the line you want to select the text.
-   Press **V** (upppercase `V`) to state Visual line mode.
-   Select multiple line using arrow or j/k keys.
-   Press `:` after selecting the desired lines. This will open the line range command `:'<,'>`.
-   Type `s/$/value/`, where "value" is the text you want to add at the beginning of each fo the selected lines. `($)` indicats the will be added to the end of the line.


#### Example {#example}

Adding comments `;` to each line in verilog.

```vim
:'<','>s/$/; /
```


## Removing Text at the End of Multiple Line {#removing-text-at-the-end-of-multiple-line}

-   Press `Esc` to navigate to normal mode.
-   Press **V** (upppercase `V`) to state Visual line mode.
-   Select multiple line using arrow or j/k keys.
-   Press `:` after selecting the desired lines. This will open the line range command `:'<,'>`.
-   Type `s/..$//`, where `($)` indicates the **end** of the line and `(..)` will select excatly two characters and `(//)` will delete them.


#### Example 1: Delete the first two characters from the selected lines {#example-1-delete-the-first-two-characters-from-the-selected-lines}

Remove the comments `//` for the selected lines.

```vim
:<','>s/..$//
```


#### Example 2: Delete the first two character from all the lines in a file {#example-2-delete-the-first-two-character-from-all-the-lines-in-a-file}

```nil
:%s/..$//
```

-   Ensure you in the normal by pressing `Esc` before executing the above command.
-   `%` applies the command to all the line in the file.
