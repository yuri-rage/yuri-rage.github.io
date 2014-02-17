---
layout: post
title: "Doctoring Jekyll"
tagline: "Do you even Jekyll, bro?"
description: ""
category: "geekery"
tags: [jekyll, linux, github]
date: "Sat, 15 Feb 2014 21:55:19 +0300"
---
{% include JB/setup %}

Here's how I improved upon the bootstrapped site that I cloned from [Jade Dominguez's](http://jekyllbootstrap.com) GitHub repository.  I used the [Hooligan theme](http://themes.jekyllbootstrap.com/preview/hooligan) - you'll see it throughout the code samples.  However, I doubt that any of these hacks are theme-unique.

##Add a post list to the site homepage##
This bit of magic will list all of your posts, starting with the most recent, with links to their titles, each followed by the first 75 words.  Jekyll does support the `{% raw %}{% excerpt %}{% endraw %}` liquid tag, allowing you to use `<!--more-->` separators in your posts if you so choose.  Though perhaps less elegant, I thought this was simpler.  Here is everything that follows the YAML FrontMatter in my `index.md`:

    {% raw %}{% include JB/setup %}

    <div class="blog-index">  
      {% for post in site.posts %}
      <h2><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.content | strip_html | truncatewords:75 }}<br>
      <a href="{{ post.url }}">Read more...</a><br><br>
      {% endfor %}
    </div>{% endraw %}

##Add a social media icon##
The theme includes a few standard social media icons that can be displayed in the upper right of the site's navigation bar.  While a YouTube channel button is included in `assets/themes/hooligan/css-social-buttons/`, it isn't enabled in the nav-bar by default.  Let's fix that.

First, add a YouTube line to the author section of `_config.yml`:

    author : 
      name : [your name]
      email : [email address]
      github : [user name]
      twitter : [user name]
      feedburner : [user name]
      youtube : [user/channel name]

Next, add the following markup to `_includes/themes/hooligan/default.html`:

    {% raw %}{% if site.author.youtube %}
      <li>
        <a href="http://www.youtube.com/user/{{ site.author.youtube }}" class="zocial youtube icon" target="_blank">
        <span class="hidden-desktop">YouTube</span>
        </a>
      </li>
    {% endif %}{% endraw %}

The location for that last bit should be obvious - amongst other similarly listed social media icons.  If you did it correctly, a theme-formatted YouTube icon should appear in your site's nav-bar, linked to your channel.

##Pretty up those YouTube embeds##
While we're on the subject of YouTube, let's talk responsive content.  If you simply copy and paste the embed markup from a YouTube video, it won't pass muster with Jekyll's markup engine.  At a minimum, you'll need to delete the allowfullscreen keyword, and put a space before `</iframe>` in order to avoid site build errors.  Once you've done that, you'll get an ugly, fixed size frame that might look ok on a full-sized monitor, but will wreak havoc with page displays on tablets and mobile phones.  One solution for auto-resizing YouTube frames is to make a [Jekyll plugin using a Ruby script](http://brettterpstra.com/2013/01/20/jekyll-tag-plugin-for-responsive-youtube-video-embeds).  Unfortunately, as of this writing, [GitHub pages do not support Jekyll plugins](https://help.github.com/articles/pages-don-t-build-unable-to-run-jekyll).  Let's try [something else](http://css-tricks.com/NetMag/FluidWidthVideo/Article-FluidWidthVideo.php).

First, create your own style sheet.  I called mine, `assets/yuri.css`:

    /* video wrapper - added by yuri */
    /* credit: http://css-tricks.com/NetMag/FluidWidthVideo/Article-FluidWidthVideo.php */
    .videoWrapper {
	  position: relative;
	  padding-bottom: 56.25%; /* 16:9 */
	  padding-top: 25px;
	  height: 0;
    }
    .videoWrapper iframe {
	  position: absolute;
	  top: 0;
	  left: 0;
	  width: 100%;
	  height: 100%;
    }

Now, create your own HTML include file.  Mine is `_includes/yuri-style.html`:

    <link href="/assets/yuri.css" rel="stylesheet" type="text/css" media="all">

    <!-- fav and touch icons -->
    <link rel="shortcut icon" href="images/favicon.ico">
    <link rel="apple-touch-icon" href="images/apple-touch-icon.png">
    <link rel="apple-touch-icon" sizes="72x72" href="images/apple-touch-icon-72x72.png">
    <link rel="apple-touch-icon" sizes="114x114" href="images/apple-touch-icon-114x114.png">

Note, I included favicon and touch icon markup as well.  [Here's a brief tutorial on creating icons](http://richmartel.com/articles/favicon.php).

Finally, add a liquid include tag in the `<head>` section of `_includes/themes/hooligan/default.html`:

    {% raw %}{% include yuri-style.html %}{% endraw %}

That last bit isn't the most elegant of solutions, as I had to edit a theme file (again), but if I ever change themes, I suppose I'll just reread my own tutorial!  To be complete, here's an example of properly (for Jekyll) formatted embed markup:

    <div class='videoWrapper'>
    <iframe allowfullscreen='allowfullscreen' frameborder='0' height='315' src='//www.youtube.com/embed/lWOiGbt9tKg' width='560'> </iframe>
    </div>

Note the space in front of `</iframe>`.  Also note the allowfullscreen bit (which can just as easily be deleted entirely).  For now, I have to remember to wrap them in a videoWrapper section and make sure the formatting meets Jekyll's picky syntax expectations.   At some point, I may add a Rakefile task to simplify YouTube embeds.  Look for a post in the near future about my Jekyll-specific Rakefile tasks.
