---
title: "About"
layout: page-sidebar
permalink: "/about.html"
comments: false
---
{% assign author = site.authors['ryan'] %}

<img style="float: left; width: 200px; margin-right: 30px;" src="{{ site.url }}{{ author.picture | relative_url }}" alt="{{ author.display_name }}">
My name is Ryan Ververs-Bijkerk and I have created Logit Blog in November 2010. I use this blog to share knowledge and experience from the field.

I'm currently a Technical Evangelist at RawWorks focused on the End-User Computing area. I primarily focusing on the user experience in centralized desktop environments. With my development skills, I'm capable to quickly adapt to customers by having an agile mindset. Yes, I'm a bit lazy, yes lazy but in a good way. I'm all about automation as I'm not made for repeating tasks.

Community is very important for me as I'm an active blogger and has published multiple tools. Next, to this personal blogs and tools, I'm also a member of the Remote Display Analyzer team and TeamRGE. I have been awarded several times for Microsoft Most Valuable Professional (MVP) and currently holds the Citrix Technology Professional (CTP) and VMware vExpert status.

If you ever meet me you can always do me a pleasure buying a beer because I love beer. Besides that, I also enjoy sim racing and playing games like Rainbow Six Siege.

You can follow me on Twitter or get in touch via LinkedIn.
<div>
    <span>
        {% if author.twitter %}
        <a target="_blank" href="{{ author.twitter }}" class="btn btn-outline-success btn-sm btn-round ml-2"><i style="font-size: 24px;" class="fab fa-twitter"></i></a>
        {% endif %}
        {% if author.linkedin %}
        <a target="_blank" href="{{ author.linkedin }}" class="btn btn-outline-success btn-sm btn-round ml-2"><i style="font-size: 24px;" class="fab fa-linkedin"></i></a>
        {% endif %}
        {% if author.github %}
        <a target="_blank" href="{{ author.linkedin }}" class="btn btn-outline-success btn-sm btn-round ml-2"><i style="font-size: 24px;" class="fab fa-github"></i></a>
        {% endif %}
        {% if author.reddit %}
        <a target="_blank" href="{{ author.linkedin }}" class="btn btn-outline-success btn-sm btn-round ml-2"><i style="font-size: 24px;" class="fab fa-reddit"></i></a>
        {% endif %}
    </span>
</div>
