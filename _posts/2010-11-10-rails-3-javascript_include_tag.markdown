---
author: zaki
date: '2010-11-10 01:55:18'
layout: post
type: code
slug: rails-3-javascript_include_tag
status: publish
title: Rails 3 javascript_include_tag
wordpress_id: '511'
comments: true
tags:
  - rails
  - ruby
---

<div class="alert-message block-message info">
  The below issue is already fixed in rails.
</div>

After installing the _jquery_ujs _driver for a Rails 3 app, today it started
acting up when I tried using the Cycle jQuery plugin. Specifically, the
following code in application.js would create twice as many pager anchors as
expected.

{% highlight js %}
$(document).ready(function(){
  $('.featured').cycle({
      fx: 'fade',
      timeout: 5000,
      speed: 600,
      pager: '#featured-controls',
      pauseOnPagerHover: true,
      pause: true,
      pagerAnchorBuilder: function(idx, slide) { return '<a href="#">'; }
  });
});
{% endhighlight %}

At first I thought it was a problem with my HTML code as I was using nested
_divs _(by chance two internal _divs _per container _div _I wanted to cycle),
but that wasn't the reason. Tracing the problem, I noticed, that
application.js would get executed twice, and lo and behold, there it is at the
top of the source: application.js included twice. Hmm, that's strange, my
includes look like this:

{%highlight erb%}
<%= javascript_include_tag :defaults %>
<%= javascript_include_tag 'jquery.cycle.min' %>
{%endhighlight%}

In application.rb, I made the following configuration as suggested by the
[jquery_ujs](https://github.com/rails/jquery-ujs) github's README:

{%highlight ruby%}
config.action_view.javascript_expansions[:defaults] = %w(jquery rails application)
{%endhighlight%}

As it turns out, however, rails is very smart about javascript files and when
someone includes **:defaults **(and in :defaults only) it will automatically
append application.js to the list of files to include. Let's take a look at
**_actionpack/lib/action_view/helpers/asset_tag_helper.rb_**:

{%highlight ruby%}
def expand_javascript_sources(sources, recursive = false)
  if sources.include?(:all)
    all_javascript_files = collect_asset_files(config.javascripts_dir, ('**' if recursive), '*.js')
    ((determine_source(:defaults, @@javascript_expansions).dup & all_javascript_files) +
      all_javascript_files).uniq
  else
    expanded_sources = sources.collect do |source|
      determine_source(source, @@javascript_expansions)
    end.flatten
    expanded_sources << "application" if sources.include?(:defaults) && File.exist?(File.join(config.javascripts_dir, "application.js"))
    expanded_sources
  end
end
{%endhighlight%}

Removing _application _from the **javascript_expansions[:default]
**configuration removes the duplicate application.js and Cycle now works
properly.

