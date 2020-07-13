---
layout: post
hidden: false
title: Moved away from WordPress to GitHub pages
authors: [ryan]
categories: [GitHub]
tags: [Blog, GitHub Pages, Jekyll]
image: assets/images/posts/2020-07-12-moved-away-from-wordpress-to-github-pages/moved-away-from-wordpress-to-github-pages-feature-image.png
---
It took me some time to move all the posts, but it has been done. Goodby WordPress and hello Github pages! Other the last 10 years Logit Blog has been hosted on a small hosting provider in the Netherlands running WordPress. On my site I have experiences a lot of errors and even crashes of my entire site when upgrading plugins or to the latest version of WordPress. I'm done with that, so time to move on! This blog post will share some details and information how I moved not only Logit Blog but also GO-EUC to Github pages.

## What are the benefits of Github pages?
There are couple benefits when moving to Github pages, the first I want to highlight is performance. Github pages is based on Jekyll which is a engine to generate static websites or blogs. This result in a website that loads very fast, as the content is static. As Github has a content distribution network the website is always served from the closeted location near you, resulting in a fast experience.

Github pages does not rely on any backend component like MySQL which reduces the attack serve. There are no plugins available, which may be a downside, but this also ensures the security of the website. Plugins may be malicious which can affect the security, but also the experience of your website.

Another important factor is cost, there is no hosting fee to run your website on Github pages. The only cost you will have is the domain name it self. Now it is important to mention the following:

>GitHub Pages is not intended for or allowed to be used as a free web hosting service to run your online business, e-commerce site, or any other website that is primarily directed at either facilitating commercial transactions or providing commercial software as a service (SaaS).
> 
> Source: [docs.github.com](https://docs.github.com/en/github/working-with-github-pages/about-github-pages#prohibited-uses){:target="_blank}

The last benefit is collaboration, although this does not apply to my personal blog, this is very applicable to [GO-EUC](https://www.go-euc.com){:target="_blank"}. Github pages makes it easy to collaborate using Pull requests. This way you don't have to deal with creating and securing accounts, as this is all done in Github. From my own experience, it is important to have a basic understanding of Git and Github functionalities, otherwise it might feel complex.

## Creating a theme
The look and feel of a website is very important. My goal was for both my personal blog as GO-EUC to have the same experience using a similar theme. There are many themes available on various websites. Here are a couple of examples:

  * [jamstackthemes.dev](https://jamstackthemes.dev/ssg/jekyll/){:target="_blank"}
  * [jekyllthemes.org](http://jekyllthemes.org/){:target="_blank"}
  * [jekyllthemes.io](https://jekyllthemes.io/){:target="_blank"}
  * [jekyll-themes.com](https://jekyll-themes.com/){:target="_blank"}

There are a couple thinks you need to take into account when selecting a theme. It is important to understand not all Jekyll functionalities are supported on Github pages. It is therefore important to understand the compatibility with Github pages or accept the experience is not the same as the example. You have to keep in mind the site will be a static site, this means there are some limitations. For example, at GO-EUC we have our upcoming event list. This is generated based on the dates, but not automatically updates once the event is over. The site needs to be compiled again to hide past events.

To get the look and feel you want it does require HTML and CSS skills.

## Run the website locally
When adjusting the theme to your desire, it is recommended to run the website locally. This allows you to quickly validate all the changes. The quick start from [Jekyll documentation site](https://jekyllrb.com/docs/){:target="_blank} explains how to get up and running. When running Windows 10, it is possible to use the "Windows Subsystem for Linux", this way it is possible to run Ubuntu.

![ubuntu-windows-jekyll-serve]({{site.baseurl}}/assets/images/posts/2020-07-12-moved-away-from-wordpress-to-github-pages/ubutnu-windows-jekyll-serve.png)

When running the website, the page can be accessed using http://locahost:4000. It is important to understand some functionalities will work locally, but might not work on Github pages.

## Moving from WordPress to Github pages
There are multiple guides and methods available to migrate your content from WordPress to Github pages. There is a [Jekyll export plugin](https://wordpress.org/plugins/jekyll-exporter/){:target="_blank"} available for WordPress, but I never got that one working. By default WordPress have an export functionality, which allows you to export all the content to an XML format. Login to WordPress and go to *Tools > Export*, there you can select what content can be exported.

In order to convert everything to Markdown format, I have used a tool called [WpXml2Jekyll](https://github.com/theaob/wpXml2Jekyll/releases){:target="_blank"} by [Onur Baykal](https://github.com/theaob){:target="_blank"}. This tool will convert everything to the markdown format. It is very important to validate each individual post to ensure the formatting and layout.

In my case, I had the original images so I was able copy those over, but otherwise you need to ensure all images are downloaded and available.

## Markdown format
Markdown is a lightweight markup language for plain-text formatting, created back in 2004 by John Gruber with Aaron Swartz. Comparing to WordPress, this does require some adjustments in order to getting used to the format. Luckily there are lot of guides and cheat sheets available to can help you out. A couple examples:

  * [Markdown guide](https://www.markdownguide.org/basic-syntax/){:target="_blank"}
  * [Github Guides](https://guides.github.com/features/mastering-markdown/){:target="_blank"}
  * [Markdown Here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet){:target="_blank"}

Github Pages does allow you to embed iframe or other sources like Twitter. Many platforms have a embed functionality that allows you to integrate this on the site. The embed HTML can be pasted directly in a post. For example: 

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Ready to go for the our digital <a href="https://twitter.com/E2EVC?ref_src=twsrc%5Etfw">@E2EVC</a> session with <a href="https://twitter.com/eltjovg?ref_src=twsrc%5Etfw">@eltjovg</a> <a href="https://twitter.com/hashtag/GOEUC?src=hash&amp;ref_src=twsrc%5Etfw">#GOEUC</a> <a href="https://t.co/9KczJGC0Xz">pic.twitter.com/9KczJGC0Xz</a></p>&mdash; Ryan Ververs-Bijkerk (@Logitblog) <a href="https://twitter.com/Logitblog/status/1271432965822844928?ref_src=twsrc%5Etfw">June 12, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Running your website locally can help validate the markdown of your post to ensure everything looks Ok. Another way to validate the post, is to set the post to hidden. In the page front matter, you can add "hidden: true" to hide the post. This way the post is not listed on the site, but is still accessible using the URL.

## Conclusion
Although I was quite happy using WordPress, the heavy maintains and hosting cost eventually moved me to Github pages. Github pages is a great platform to run your blog with low cost and great performance. Of course, there are some downsides. As the website is static, there are some limitations in dynamic content and writing in Markdown format requires time to getting used to. But at the end, moving your site to Github pages will benefit in performance, security and allows better collaboration.

Big credits to [Aaron Parker](https://twitter.com/stealthpuppy){:target="_blank"} and [Bill Raymond](https://twitter.com/billraymonde){:target="_blank} sharing knowledge on Github pages. You both inspire me to make this move. If you are looking for a great resource on getting started with Jekyll, Bill created a [video series](https://www.youtube.com/playlist?list=PLWzwUIYZpnJuT0sH4BN56P5oWTdHJiTNq){:target="_blank"} on this topic.

If you have any questions or if you need help, feel free to reach out or leave a comment below.

<span>Photo by <a href="https://unsplash.com/@ninjason?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank">Jason Leung</a> on <a href="https://unsplash.com/s/photos/party?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank">Unsplash</a></span>