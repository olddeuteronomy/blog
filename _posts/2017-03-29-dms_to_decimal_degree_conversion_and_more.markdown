---
layout: post
title: "DMS To Decimal Degree Conversion And More"
comments: true
tags: emacs calc
---

I'm working in the field of satellite remote sensing and satellite
data processing. Sometimes I need to convert a coordinate (or an
angle) in the DMS notation (degree@minute'second") to a decimal degree
(a floating point number) and vice versa.

Of course, I want to do this right in an Emacs buffer, so I wrote a
simple function using `deg` and `hms` functions from the Emacs'
built-in **calc** package:

{% highlight elisp %}
(defun dms2deg (p1 p2)
  "Converts a region from the DMS (dd@mm'ss\") to a decimal degree.
P1,P2 - beginning and end of the region respectively."
  (interactive "r")
  (let (s)
    (if (region-active-p)
        (progn
          (setq s (calc-eval
              (concat "deg(" (buffer-substring-no-properties p1 p2) ")")))
          (delete-active-region)
          (insert s))
      (message "No active region!"))
    )
  )
{% endhighlight %}

I bound this function to the `C-f6` key sequence (Ctrl+F6):

{% highlight elisp %}
(global-set-key [(control f6)] 'dms2deg)
{% endhighlight %}

Now I can select a region in a buffer, press `C-f6` and replace the
selection with its decimal value, so `30@15'54"` becomes `30.265`.

The inverse function (bound to `S-f6` or Ctrl+F6) replaces a floating
point with the DMS string:

{% highlight elisp %}
(defun deg2dms (p1 p2)
  "Converts a region from a decimal degree to the DMS (dd@ mm' ss\").
P1,P2 - beginning and end of the region respectively."
  (interactive "r")
  (let (s)
    (if (region-active-p)
        (progn
          (setq s (calc-eval
              (concat "hms(" (buffer-substring-no-properties p1 p2) ")")))
          (delete-active-region)
          (insert s))
      (message "No active region!"))
    )
  )

(global-set-key [(shift f6)] 'deg2dms)
{% endhighlight %}

After a while, I decided to enhance the first conversion function by
adding an 'inline calculator' feature. It works quite simple: if a
selected region contains the '@' character, the region is considered
as a coordinate (angle) in the DMS notation and it will be *replaced*
with its decimal value. Otherwise, the region is considered as an
arithmetic expression (in terms of the Emacs' calc) and its calculated
value will be *inserted* just after the region.

{% highlight elisp %}
(defun avs/calc-region (p1 p2)
  "Calculates a region or converts DMS to decimal.
P1,P2 - beginning and end of the region respectively."
  (interactive "r")
  (if (region-active-p)
      (let ((b (buffer-substring-no-properties p1 p2)))
        (cond ((string-match-p "@" b) ; convert DMS to decimal ...
               (delete-active-region)
               (insert (calc-eval (concat "deg(" b ")"))))
              (t ; ... otherwise calculate a region
               (goto-char (region-end))
               (pop-mark)
               (insert " = " (calc-eval b))))
        )
    (message "No active region!"))
  )

(global-set-key [(f6)] 'avs/calc-region)
{% endhighlight %}

The enhanced function is bound to `f6`. So now if you press `F6`,
`15@45'20"` becomes `15.7555555556`, and `((350.0 / 7) + 48.314)`
becomes `((350.0 / 7) + 48.314) = 98.314`.

I'm sure there is still room for improvements; any ideas are welcome.
