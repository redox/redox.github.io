---
layout: post

title: How I improved Tom Preston-Werner's jekyll-based blog with 50 lines of code
link: redox.github.io/mojombo
comments: true
excerpt_image: mojombo.png
---

I recently [switched to Jekyll]({% post_url 2013-09-01-i-switched-to-jekyll %}) and was wondering how I could easily jump to old posts without displaying the famous "Last posts" menu.

<!-- more -->

What I need:

* Full-history access
* Waste less time finding old posts
* Simple UI but great UX
* If I need to type something, typo-tolerance (I make typos...)

What I don't want to do:

* Write lots of code
* Spend my money
* Have an awful list of links

### 1. Menu-based

List last posts in _ALL_ your pages, the old and ugly way.

{% highlight html %}
<ul class="last_posts">  
  {{ "{%" }} for post in site.posts limit:20 %}
     <li>  
       <span>{{ "{{" }} post.date | date_to_string }}</span> &raquo;
       <a href="{{ "{{" }} BASE_PATH }}{{ "{{" }} post.url }}">{{ "{{" }} post.title }}</a>  
     </li>  
  {{ "{%" }} endfor %}  
</ul>
{% endhighlight %}

### 2. Search-based

You may have heard about [lunr](http://lunrjs.com) and its [jekyll integration](https://github.com/slashdotdash/jekyll-lunr-js-search), you can find a demo [here](http://10consulting.com/search). It's pretty awesome but it lacks critical features for now (most critical: stemming doesn't replace typo-tolerance, matching terms are not highlighted [issue 25](https://github.com/olivernn/lunr.js/issues/25)).

It's now about 3 months since I've joined [Algolia](http://www.algolia.com) and I cannot let a day go by without trying to plug our search-engine somewhere new. Wow, my personal blog is totally "somewhere" new. Let's give it a try; Algolia is _NOT_ a site-search engine; but it could be the perfect match to easily jump on old posts.

WAIT! You said you don't want to pay anything; Algolia is a [paying service](http://www.algolia.com/pricing). You're right, just [send me an email](mailto:sylvain+from+algolia+dot+com) and I'll convert your 14-day free trial to an unlimited _custom_ free plan.

First, I need to patch my dependencies (I use a `Gemfile` and you should too :p):

{% gist 7664465 Gemfile %}

Let's make it the proper way and add all configuration stuff in my `_config.yml`.

{% gist 7664465 _config.yml %}

Reindex all post titles while publishing to Github's `gh-pages` branch.

{% gist 7664465 Rakefile %}

I used a [patched version](https://github.com/algolia/typeahead.js) of Typeahead.js to be able to use it with an Algolia backend (see the [pull-request](https://github.com/twitter/typeahead.js/pull/473)).

{% gist 7664465 layout.html %}

[Live demo](http://redox.github.io/mojombo) with [Tom Preston-Werner's blog](http://tom.preston-werner.com/), CEO of Github and creator of Jekyll. You can find the [whole patch](https://github.com/redox/mojombo/commit/070de82864fb17fb1f36f623e3136e398503370c) on Github.

<iframe src="http://redox.github.io/mojombo/" style="width: 100%; height: 600px; border: 2px solid #ccc;"> </iframe>
