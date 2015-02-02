I'm working with Jekyll and want to use `{% include... %}` to include a snippet of HTML in a markdown file without that HTML being processed further, i.e. then being handled by the markdown processor as if it were just inline HMTL.

Could this be handled with a Jekyll plugin?

I've created a GitHub repo [md-include-html](https://github.com/george-hawkins/md-include-html) with the following files that demonstrate my problem.

1\. `_layouts/default.html` contains:

    <div>
    {{ content }}
    </div>

2\. `md-page.md` contains:

    ---
    layout: default
    ---
    A

    {% include_relative html-snippet.html %}

    B

3\. `html-snippet.html` contains:

    </div>
        <!-- Some arbitrary HTML for {{ page.path }} -->
    <div>

Note that I'm first attempting to close the div that will be introduced by `_layouts/default.html` and then open a new div at the end.

This results in `_site/md-page.html`:

    <div>
    <p>A</p>

    <p>&lt;/div&gt;
        <!-- Some arbitrary HTML for md-page.md --></p>
    <div>
    ...

See how my `</div>` has been handled. If my included snippet had been something like `<div>...</div>` things would have been better - it would have been treated as inline HTML.

As far as I can see for this situation there's no way to prevent the markdown processor trying to fix what it just sees as a closing div tag without a preceding opening one.

It seems the include happens before the markdown processing and there's no way to tell the markdown processor to handle the included text differently.

So I can include markdown in a HTML file with something like this (see [`html-page.html`](https://github.com/george-hawkins/md-include-html/blob/master/html-page.html) in my repo):

    {% capture snippet_content %}
    {% include_relative md-snippet.md %}
    {% endcapture %}
    {{ snippet_content | markdownify }}

But I can't include HTML in a markdown file.

I can't see a way around this without a markdown feature (anything done in Liquid essentially happens too early) to temporarily disable markdown processing.

E.g.

    A

    <markdown-off>{% include_relative html-snippet.html %}</markdown-off>

    B

Yes I know the tags don't look very markdown-ish but you get what I mean.

Would there be some way to do this with a Jekyll plugin? I looked at those mentioned in the [plugin page](http://jekyllrb.com/docs/plugins/#available-plugins) of the Jekyll documentation but didn't spot anything immediately.
