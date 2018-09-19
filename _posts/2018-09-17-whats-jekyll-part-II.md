---
layout: post
title: What's Jekyll and How to build a personal blog using Lanyon ( Part II )
---

## Abstract

Further to the previous past,
<a href="{{ site.baseurl }}/2018-09-09/whats-jekyll-part-I/">
  What's Jekyll and How to build a personal blog using Lanyon ( Part I )
</a>, this post guides you how to add unique and useful features in your blog. Such as, linking multiple social media accounts, changing the font that you love and inserting google analytics in your website.

## Instructions

<h4><a name="TableContent"></a> Table of Contents</h4>

<h6><ol> 
	<li><a href=""></a></li>
</ol></h6>

<h4><a name="EnableAnalytics"></a>  1. Enable Google Analytics</h4>

Google Analytics tracking is a free web analytics service that used to analyse and track website traffic. It is now the most widely used and also really easy to add the tracking code to your Jekyll static website.

First, you most create a new account in [Google Analytics](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=2ahUKEwibhsvpisLdAhXHWrwKHRPTDPcQFjAAegQIAxAC&url=https%3A%2F%2Fanalytics.google.com%2F&usg=AOvVaw1Jx9i6a4S_nl7I67YnB98r) for this and then you will get unique tracking ID.

Now, back to your terminal, creating a new file called google\_analytics.html in \_includes directory. Next, pasting the following Google Analytics tracking code in this file and save it.

<div class="language-shell highlighter-rouge"><pre class="highlight"><code> 
<p><script></p>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1\*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

  ga('create', '{{ site.google\_analytics }}', 'auto');
  ga('send', 'pageview');
</script>
</code></pre></div>




<h4><a name="InsertDisqus"></a>  2. Inserting Disqus Comments</h4>





<h4><a name="AddArchive"></a>  3. Adding a archive Page</h4>





<h4><a name="AddSocial"></a>  4. Adding Social Media Buttons</h4>





<h4><a name="AddPhoto"></a>  5. Adding your Photo to the sidebar</h4>






## Reference
[1] [How to build a blog using Github, Jekyll and Lanyon](https://nikhita.github.io//build-blog-using-github-jekyll#using-lanyon), by Nikhita Raghunath

[2] [Google Analytics for Jekyll](https://desiredpersona.com/google-analytics-jekyll/)

## Note
<p>If you have any constructive criticism or advises, leave the comments below or feel free to email me @qazqazqaz850@gmail.com.

Hope this post will help! :)
</p>
