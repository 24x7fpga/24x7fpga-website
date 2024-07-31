+++
title = "Emacs Display Image"
author = ["Kiran"]
date = 2024-05-31T12:47:00-04:00
tags = ["emacs"]
draft = false
css = "../../zcustom.css"
+++

[Reference1](https://orgmode.org/manual/Images.html), [Reference2](https://emacs.stackexchange.com/questions/10114/org-mode-no-images-to-display-inline)


## How to include an image in emacs? {#how-to-include-an-image-in-emacs}

Simple way to include images in an org document is by using the following command:

```quote
[[ ./path/to/image.png ]]
```


## How to display the image in emacs? {#how-to-display-the-image-in-emacs}

To display an inline image in emacs org file simply type " C-c C-x C-v". The same command is used to toggle the display.


## How to add caption and label to the image? {#how-to-add-caption-and-label-to-the-image}

To include the caption and label to the name it is necessary to have the image link in the separate line and add " #+caption and #+name: " before the link. See the example for reference:

```quote
#+caption: This is the caption for the image
#+name: Fig: image label
[[./imag.png]]
```
