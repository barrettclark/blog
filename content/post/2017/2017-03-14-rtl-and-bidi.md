+++
date = "2017-03-14T10:51:54-07:00"
title = "RTL scripts and BiDi webapps"
+++

A user recently submitted a Farsi translation for the Sidekiq
Web UI and I quickly merged it, happy to see another language supported.

**Unfortunately, as you know, no good deed goes unpunished.**

I quickly learned that the Farsi language uses an RTL script - it is
written from right to left.  Like a written page, webapps displaying
Farsi text should be flipped so they are RTL.

Webapps that can dynamically change text direction are called
bi-directional, or BiDi.  As you might imagine BiDi support isn't
trivial but can be done.

My i18n goal with the Sidekiq Web UI is simple: be as inclusive as
reasonably possible.  I can't speak any languages fluently except English
but our open source community has developers that speak hundreds
of languages and can help in the effort. I'm proud that the Web UI supports
20+ LTR languages today but it pains me that I can't support Farsi, Hebrew,
Arabic and other RTL languages.

[**Until now!**](https://github.com/mperham/sidekiq/issues/3381) Here's what I did.

### 1. Flip that CSS

There's a number of automated tools for scanning CSS files and outputing
flipped rules to be used when emitting RTL pages.  These rules will override
your standard LTR rules: where you specify padding-left, you want
padding-right, etc.

It helped a lot that I've kept the UI to one simple CSS file, with no
additional tooling or build process.  Bootstrap 3.x is popular enough that
other people have created flipped overrides for it already.  The only hard
work was creating application-rtl.css.

Normal CSS, application.css:

```css
.someclass { margin-left: 10px }
```

Flipped CSS, application-rtl.css:

```css
.someclass { margin-right: 10px }
```

### 2. Fix up the Generated CSS

Unfortunately it's not that easy; you'll note that now we have a
margin on **both** sides when rendering RTL.  What I had to do was go
through the generated application-rtl.css and update the rules to make
them true overrides, like so:

```css
.someclass { margin-right: 10px; margin-left: unset; }
```

Sidekiq's Web UI doesn't have a huge amount of CSS.  It took me less
than an hour to go through 1000 lines, one at a time.

### 3. Include the right CSS

Now you include your application.css as normal and optionally include
the RTL overrides if rendering an RTL script.  For example, I put this RTL logic in `<head>`:

```html
<link href="<%= root_path %>stylesheets/bootstrap.css" media="screen" rel="stylesheet" type="text/css" />
<% if rtl? %>
<link href="<%= root_path %>stylesheets/bootstrap-rtl.min.css" media="screen" rel="stylesheet" type="text/css"/>
<% end %>

<link href="<%= root_path %>stylesheets/application.css" media="screen" rel="stylesheet" type="text/css" />
<% if rtl? %>
<link href="<%= root_path %>stylesheets/application-rtl.css" media="screen" rel="stylesheet" type="text/css" />
<% end %>
```

The `rtl?` method is very simple, it just checks for Farsi as the
current locale:

```ruby
def rtl?
  'fa' == locale
end
```

The result (still buggy, note graph axis labels, but usable):

<img width="640" src="http://www.mikeperham.com/images/issue3381.png" />

### Standards

There are some upcoming standards on the horizon for easier BiDi
support in CSS so you wouldn't need overrides at all:

```css
.someclass { margin-inline-start: 10px }
```

`inline-start` maps to `right` in RTL and `left` in LTR.  Unfortunately
only Firefox supports this syntax today or I could have just updated
application.css directly.  I hope the other browser vendors can
implement this soon as it greatly eases the burden of BiDi support.

### Resources

This was an extremely brief overview of what I had to do.  Thirty minutes
of reading blog posts taught me a LOT about this subject.  Here's a few
links with more detail:

* https://hacks.mozilla.org/2015/09/building-rtl-aware-web-apps-and-websites-part-1/
* https://hacks.mozilla.org/2015/10/building-rtl-aware-web-apps-websites-part-2/

### Conclusion

I've learned after 20 years in the industry that i18n and l10n support
is fractal: there is always more complexity that causes problems (date
and number formatting, pluralization rules and text direction are just three
examples) but I make the effort so that more people can use Sidekiq
comfortably.

**This BiDi work will be part of Sidekiq 5.0.** If you see a problem in your
own language/locale, please open an issue or send a pull request to fix it.
