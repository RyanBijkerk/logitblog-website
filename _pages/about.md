---
title: "About"
layout: page-sidebar
permalink: "/about.html"
comments: false
---
{% assign author = site.authors['ryan'] %}

<img style="float: left; width: 200px; margin-right: 30px;" src="{{ site.url }}{{ author.picture | relative_url }}" alt="{{ author.display_name }}">
My name is Ryan Ververs-Bijkerk and I established Logit Blog in November 2010. I use this platform to share my knowledge and experiences from the field. As a self-employed technologist at GO-INIT, my focus is on the EUC and code area. I prioritize user experience in centralized desktop environments and use my development skills to adapt quickly to customer needs with an agile mindset. I am also known for my laziness, but in a good way as I am all about automation and dislike repetitive tasks.

Community is important to me as I am an blogger and have published multiple tools. In addition to this personal blog and tools, I am also a member of the Remote Display Analyzer team and TeamRGE. I have been recognized as a Microsoft Most Valuable Professional (MVP) multiple times and currently hold the Citrix Technology Professional (CTP), NVIDIA vGPU Community Advisor (NGCA), and VMware vExpert status.

If you ever meet me, you can always do me a favor by buying me a beer, as I love them. In my free time, I enjoy sim racing and playing games like Rainbow Six Siege. You can follow me on Twitter or connect with me on LinkedIn.
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
