---
layout: post
title: 'Hands-on: Microsoft Bot Framework with LUIS'
authors: [ryan]
comments: true
categories: [Development]
tags: [Bot, Bot, C#, Logit Bot, Microsoft Bot Framework]
image: assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/hands-on-microsoft-bot-framework-with-luis-feature-image.png
---
This year during the Microsoft Build event Microsoft has released the Bot Framework. The Bot Framework is a default template that allows you to create your own bot. But what is a bot and how does it work? The possibilities are endless and it can change the way how we interact with technology. In this blog post will explain what a bot is and cover my own hands-on experience.
<h2>What is a bot?</h2>
There are many different bots and the following video explains the difference between these bots.

<iframe width="800" height="461" src="https://www.youtube.com/embed/UQLo399K3PE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

In this case, we are focusing at a chat bot or also called a social bot. A bot is a piece of software designed to do certain tasks for you. This can be creating a reminder, sending an email or gathering information. With simulating the interaction, it feels like real human interaction. Bots are most likely found in chat applications like Slack, Skype or Facebook but you can also find them in your phone or computer, like Apple's Siri or Microsoft Cortana. There are different ways to interact with a bot. This can be with voice commands like Siri or Cortana or by text with Skype or Slack.
<h2>Getting started with the Microsoft Bot framework</h2>
As the video explains creating a bot is not rocket since anymore. The getting started documentation will guide you to create and publish a simple bot. To avoid retyping the entire documentation I provided some basic information and you can find the full getting started guide <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/gettingstarted.html">here</a>. Please note there is a minimal requirement of Visual Studio 2015 with the latest updates.

There are 2 downloads required before you start.
<ol>
 	<li>"Bot Application" template for Visual Studio 2015 (<a href="http://aka.ms/bf-bc-vstemplate">direct download</a>).</li>
 	<li>"Bot Framework Channel Emulator" (<a href="https://emulator.botframework.com/">direct download</a>).</li>
</ol>
The "Bot Application" template needs to be extracted in the user profile and this will add a new option in the "New Project" list.

<a href="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/bot-framework-vs-2015-project.png"><img class="alignnone size-medium wp-image-4094" src="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/bot-framework-vs-2015-project.png" alt="bot-framework-vs-2015-project" width="300" height="208" /></a>

The "Bot Framework Channel Emulator" is the tool to interact with your bot. The default template is a working project which will return the text received and the total amount of characters.

<a href="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/bot-framework-channel-emulator.png"><img class="alignnone size-medium wp-image-4092" src="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/bot-framework-channel-emulator.png" alt="bot-framework-channel-emulator" width="300" height="226" /></a>

<h2>Language understanding with LUIS</h2>
In order to understand the user input, we need have a kind of translation. Microsoft Reseach has created a service named LUIS "Language Understanding Intelligent Service" which can do this translation.
<blockquote>Language Understanding Intelligent Service (LUIS) offers a fast and effective way of adding language understanding to applications. With LUIS, you can use pre-existing, world-class, pre-built models from Bing and Cortana whenever they suit your purposes -- and when you need specialized models, LUIS guides you through the process of quickly building them.
LUIS draws on technology for interactive machine learning and language understanding from Microsoft Research and Bing, including Microsoft Research's Platform for Interactive Concept Learning (PICL). LUIS is a part of a project of Microsoft Cognitive Services.

Source: <a href="https://www.luis.ai/Home/About">luis.ai</a></blockquote>

<h2>My creation named Logit Bot</h2>
To get some experience I have created a bot named Logit Bot. My goal is to provide another way to get the latest publications from my blog. Like many other blogs, mine is based on WordPress which has a rich library of plugins. In order to query and receive the latest posts, I installed the plugin "JSON API". This allows me to directly query my own blog using JSON web API.

Two retrieve the publications in the code I have created two methods. One for receiving the latest publication and another for search a specific topic.

GetPosts will query my blog for the latest posts. By default, the API query will resolve 5 posts. To limit the results I have added the "count=1" is added to the URI path.

<pre class="lang:c# decode:true">        public async Task&lt;Rootobject&gt; GetPosts()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri("http://www.logitblog.com/");
            client.DefaultRequestHeaders.Accept.Clear();
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var path = "api/get_posts?count=1";
            Rootobject post = new Rootobject();

            HttpResponseMessage response = await client.GetAsync(path);
            if (response.IsSuccessStatusCode)
            {
                post = await response.Content.ReadAsAsync&lt;Rootobject&gt;();
            }

            return post;
        }
</pre>

In order to search for a specific topic, I also added a method named SearchPosts. This method will use the "get_search_results" API with a specific query. For now I have limited the results with a maximum of 30 posts. A full query will look like "api/get_search_results/?search=docker&amp;count=30"

<pre class="lang:c# decode:true">        public async Task&lt;Rootobject&gt; SearchPosts(string search)
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri("http://www.logitblog.com/");
            client.DefaultRequestHeaders.Accept.Clear();
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var path = $"api/get_search_results/?search={search}&amp;count=30";
            var posts = new Rootobject();
            HttpResponseMessage response = await client.GetAsync(path);
            if (response.IsSuccessStatusCode)
            {
                posts = await response.Content.ReadAsAsync&lt;Rootobject&gt;();
            }

            return posts;
        }
</pre>

By using LUIS the input message is validated and based on the intent the action is selected.

<pre class="lang:c# decode:true">                var entity = await _luisService.GetEntity(activity.Text);
                if (entity.intents.Any())
                {
                    switch (entity.intents[0].intent)
                    {
                        case "blog.intro":
                            messageString = await CreateIntroMessage();

                            break;

                        case "blog.owner":
                            messageString = await CreateOwnerMessage();

                            break;

                        case "blog.authors":
                            messageString = await CreateAuthorsMessage();

                            break;

                        case "blog.post":
                            var post = await _postsService.GetPosts();
                            messageString = await CreateCountPostMessage(post);

                            break;

                        case "blog.post.search":

                            if (!entity.entities.Any())
                            {
                                break;
                            }

                            var postsSearch = await _postsService.SearchPosts(entity.entities[0].entity);
                            messageString = await CreateSearchPostMessage(postsSearch);

                            break;

                        default:
                            messageString = "Sorry I don't understand...";

                            break;
                    }
                }
</pre>

I have shared the code on GitHub so if you want to see more of the code you can check it right <a href="https://github.com/RyanBijkerk/logtiblog-bot" target="_blank" rel="noopener">here</a>.

In order to integrate with a Skype or any other channel, you need to publish the bot public. Because I have a Microsoft Azure subscription I have decided to publish my bot in Azure. I have one remark, the Microsoft getting started guide states to update the "MicrosoftAppId/Password" in the web.config. This can also be done in the application settings within Azure so these are not stored within the code repository.

<img class="alignnone size-medium wp-image-4087" src="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/logit-bot-azure-settings.png" alt="logit-bot-azure-settings" width="300" height="224" />

Once the bot is public, you should be able to register your bot at the <a href="https://dev.botframework.com/#/bots/new" target="_blank" rel="noopener">Bot Framework website</a>.

<a href="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/logit-bot-register.png"><img class="alignnone size-medium wp-image-4090" src="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/logit-bot-register.png" alt="logit-bot-register" width="300" height="280" /></a>

Once registered you should be able to test it with Skype which looks like this.

<a href="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/logit-bot-introduction.png"><img class="alignnone size-medium wp-image-4088" src="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/logit-bot-introduction.png" alt="logit-bot-introduction" width="300" height="159" />     </a>

<a href="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/logit-bot-posts.png"><img class="size-medium wp-image-4089 alignnone" src="{{site.baseurl}}/assets/images/posts/2016-11-29-hands-on-microsoft-bot-framework-with-luis/logit-bot-posts.png" alt="logit-bot-posts" width="215" height="300" /></a>

In order to get your bot in the bot directory, you need to publish your bot. I did not achieve this because there are legal requirements for a term of use and privacy policy. While my bot is not published yet you can still try it out on Skype! Click on the "Add to Skype" button to add the Logit Bot to your contact list and let me know what you think!

<blockquote><strong>Update:</strong> Logit Bot is currently offline to reduce cost on my Azure account.</blockquote>

<h2>Conclusion</h2>
A Bot is a very elegant way to have simple human-like interaction to achieve certain tasks. There is a lot of potential in this kind of technology in various scenarios. Bots can change how we interact with helpdesks to resolve basic issues like a password reset or to create a ticket. From a consumer perspective, it can check the current bank balance or submit a transaction.

From my experience, it is quite easy to create a very basic bot. With a service like LUIS a bot is getting another dimension. As mentioned at the start the possibilities are endless but as the video explains it also contains a certain risk. I'm very curious if this trend will evolve in a standard. I hope this blog post was useful and inspiring and please share your thoughts and ideas of bots in the comment below.
