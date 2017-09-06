---
layout: post
title: "Select To End Of Line And Copy To Clipboard"
comments: true
tags: emacs elisp
---

As we know, `(kill-line)` command (bound to `C-k` by default) kills rest of line, beginning from the current point (cursor position). However I was not able to find a command in the default Emacs command set which *selects* from the current point to end of line (certainly you can do that with `C-SPC C-e`, but I'd like to save on keypresses). After some searching, I found a short and elegant solution at [StackOverflow](https://emacs.stackexchange.com/questions/10254/select-text-to-end-of-line-without-moving-point):

{% highlight elisp %}
(defun mark-from-point-to-end-of-line ()
  "Mark everything from point to end of line."
  (interactive)
  (set-mark (line-end-position))
  (activate-mark))
{% endhighlight %}

This function keeps the selection and you can now either copy or kill it or even expand or shrink the region using cursor movement keys.

A slightly modified version of this functions allows you to *copy* the rest-of-line region to the clipboard:

{% highlight elisp %}
(defun mark-from-point-to-end-of-line-and-copy ()
  "Mark everything from point to end of line and copy to clipboard."
  (interactive)
  (set-mark (line-end-position))
  (activate-mark)
  (kill-ring-save (region-beginning) (region-end)))
{% endhighlight %}

Please note that the region has lost highlighting in this case.

I decided to have both versions and bound them to `C-c w` and `C-e` respectively (it seems `C-e` is not bound to any command by default).

{% highlight elisp %}
(global-set-key (kbd "C-c w") #'mark-from-point-to-end-of-line)
(global-set-key (kbd "C-e") #'mark-from-point-to-end-of-line-and-copy)
{% endhighlight %}
