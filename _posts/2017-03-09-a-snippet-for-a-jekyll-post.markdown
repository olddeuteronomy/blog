---
layout: post
title:  "A Snippet For A Jekyll Post"
date:   2017-03-09 19:06:32 +0300
comments: true
tags: emacs snippet jekyll
---

I use Jekyll for writing this blog. Clearly, I
use
[yasnippet](http://ergoemacs.org/emacs/yasnippet_templates_howto.html)
package to automate everything possible. The following snippet
(`~/.emacs.d/snippets/markdown-mode/post`) is used as the Front Matter
of every post:

{% highlight snippet %}
# -*- mode: snippet -*-
# name: Jekyll post template
# key: \post
# --
---
layout: post
title:  "${1:Title}"
date:   `(format-time-string "%Y-%m-%d %H:%M:%S %z")`
comments: true
tags: ${2:emacs}
---


{% endhighlight %}

It expands to

{% highlight markdown %}
---
layout: post
title:  "Title"
date:   2017-03-09 19:06:32 +0300
comments: true
tags: emacs
---


{% endhighlight %}

After that just edit the title and add some tags separated by space.
