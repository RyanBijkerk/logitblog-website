---
layout: post
title: String.ToLower and the dotless i
authors: [dennis]
comments: true
categories: [Development]
tags: [C#, Development, i, Turkish regional settings]
image: assets/images/posts/2012-08-13-string-tolower-and-the-dotless-i/string.tolower-and-the-dotless-i-feature-image.png
---
A project I am working on uses the following C# code to compare strings:
<a href="{{site.baseurl}}/assets/images/posts/2012-08-13-string-tolower-and-the-dotless-i/code_fragment.png"><img class="alignnone size-full wp-image-1755" title="code_fragment" src="{{site.baseurl}}/assets/images/posts/2012-08-13-string-tolower-and-the-dotless-i/code_fragment.png" alt="" width="532" height="196" /></a>
This code works perfectly except when users use the Turkish regional settings. Suddenly the code stops working when FieldName = <span style="color: #a31515;">"Issued"</span>;

The <span style="color: #0000ff;">case</span><span style="color: #a31515;"> "issued"</span>: section is never hit. So I start troubleshooting.
<a href="{{site.baseurl}}/assets/images/posts/2012-08-13-string-tolower-and-the-dotless-i/troubleshoot.png"><img class="alignnone size-full wp-image-1756" title="troubleshoot" src="{{site.baseurl}}/assets/images/posts/2012-08-13-string-tolower-and-the-dotless-i/troubleshoot.png" alt="" width="245" height="78" /></a>
That is weird. FieldName to lower case is ıssued but that doesn’t equal issued. What is going on here? Let’s have a look at the ASCII codes for these two strings.
<a href="{{site.baseurl}}/assets/images/posts/2012-08-13-string-tolower-and-the-dotless-i/ASCII.png"><img class="alignnone size-full wp-image-1757" title="ASCII" src="{{site.baseurl}}/assets/images/posts/2012-08-13-string-tolower-and-the-dotless-i/ASCII.png" alt="" width="677" height="342" /></a>
Wait a second. The first character is ASCII code 305? It turns out that the Turkish alphabet distinguish between a dotted and dotless i. ASCII code 305 is a lowercase dotless i.

Looking at the ASCII codes for both words makes it clear why the case section was never hit. The fix was fairly easy at that point:
<a href="{{site.baseurl}}/assets/images/posts/2012-08-13-string-tolower-and-the-dotless-i/solution.png"><img class="alignnone size-full wp-image-1758" title="solution" src="{{site.baseurl}}/assets/images/posts/2012-08-13-string-tolower-and-the-dotless-i/solution.png" alt="" width="548" height="21" /></a>
The InvariantCulture parameter is essentially telling the ToLower function to ignore language/country/region culture information when converting to lower case. The documentation says the following about the <a href="http://msdn.microsoft.com/en-us/library/4c5zdc6a.aspx">InvariantCulture property</a>, emphasis mine.
<p style="padding-left: 30px;"><em>The InvariantCulture property represents neither a neutral nor a specific culture. It represents a third type of culture that is culture-insensitive. It is associated with the English language but not with a country or region. Your applications can use this property with almost any method in the System.Globalization namespace that requires a culture. However, <strong>an application should use the invariant culture only for processes that require culture-independent results, such as formatting and parsing data that is persisted to a file</strong>. In other cases, it produces results that might be linguistically incorrect or culturally inappropriate.</em></p>
If you zoom in to the result returned from FieldName.ToLower(); you will see that the i actually lacks a dot. I overlooked that in my troubleshooting and got stuck wondering why issued would not equal issued.

More information about the dotless i:
<a href="http://webdesign.about.com/od/localization/l/blhtmlcodes-tr.htm">http://en.wikipedia.org/wiki/Dotted_and_dotless_I
http://webdesign.about.com/od/localization/l/blhtmlcodes-tr.htm</a>
