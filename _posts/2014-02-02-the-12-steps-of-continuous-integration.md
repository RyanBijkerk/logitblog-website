---
layout: post
title: The 12 steps of Continuous Integration
authors: [marc]
comments: true
categories: [Development]
tags: [Agile, Ant, CI, Continuous Integration, Development, Development, Jenkins, Kanban, Lean, Phing, Scrum, TeamCity, TFS]
image: assets/images/posts/2014-02-02-the-12-steps-of-continuous-integration/the-12 steps of-continuous-integration-feature-image.png
---
A startup always starts with a good idea. A few people got an idea and are so eager about it that they want to give it a chance. After doing some research they find out that the idea, an application, is indeed a one-of-a-kind and it’s potentially sellable. The group starts programming and within two months the first version of the application is released. The community is reacting enthusiastic on the trial version of the software and there are already some buyers. The contacts with the community are good and bugs are fixed almost immediately. Meanwhile, a lot of new ideas are popping up and the application is evolving. The project members are popping champagne bottles and celebrate… too early. In this blog post I will tell you all about continuous integration which will get your software to the next level.
<blockquote>A lot of startups with potential are celebrating their succes too early...</blockquote>
Because of its evolution the software is getting more complex. More bugs are popping up and when some part of the application is fixed another part is suddenly bugged. The project members have their hands full fixing the broken parts, buyers are requesting more and more new features and the pressure of the community is rising. Deadlines and promises are not met and the programmers are getting demotivated. The once so great idea is turning into a disaster.

The phase right after the startup is the one where a software startup often fails. There are of course a dozen of possible causes. For example, the testing of the software is done manually by the programmer all the time, but because this is the boring part of programming, it is often skipped. Because the software is so complex, quality assurance is now taking a great amount of time, but it seems to be less important. But it is important. Maybe the most important. Popular management philosophies like Lean and Agile dictate it for you: stop the process workflow, first remove the bottleneck. In this particular the lack of Quality Assurance is the bottleneck. So let’s remove it by automating the boring test process and start Continuous Integration.

I created a series of 12 steps that hopefully helps you to take testing to another level. This process is often called the development pipeline where you put in the source code of your application at the beginning and receive a potential releasable package at the end.

<a href="{{site.baseurl}}/assets/images/posts/2014-02-02-the-12-steps-of-continuous-integration/development-pipeline.png"><img class="alignnone size-full wp-image-2734" src="{{site.baseurl}}/assets/images/posts/2014-02-02-the-12-steps-of-continuous-integration/development-pipeline.png" alt="Development Pipeline" width="828" height="98" /></a>

<strong>1. Version management</strong>
Many developers are using version management tools like Git or Subversion. It often happens that developers don’t respect the guidelines of building a repository. They just use it as a backup method. Don’t do that. Your trunk is your holy grail. Keep it as clean as possible with only source code and configuration files. Don’t add large third party setup files or additional content like movies or photo’s. The trunk should always contain the latest healthy code that works when it gets checked out. There are many guidelines available <a href="https://www.google.nl/?gws_rd=cr&amp;ei=sn_lUujOJqSm0AWRpIDQBQ#q=development+subversion+guidelines" target="_blank">online</a> which can help you how to create your repository.

<strong>2. Decide a development strategy</strong>
Decide what kind of development strategy you want to use. For example, SCRUM or Kanban can be used to develop in small increments and integrate perfectly with Continuous Integration. When you choose a development strategy make sure everybody understands and accepts the strategy. When this is not the case it is likely that the process is disturbed because of communication problems.

<strong>3. Decide your delivery strategy</strong>
Depending on what you are building, you choose a delivery strategy. The delivery is the result at the end of the development pipeline. Continuous Delivery is ideal for application development: the result can for example be an installation package which directly can be delivered to the QA tester or customer. It also can be used in web development where the result can be an update in the acceptance or production environment.

<strong>4. Decide an application version strategy</strong>

The version of your software communicates the changes and is normally used to inform your clients of the latest updates. In large projects an example of a version number with four digits, like 5.1.2.276 can be:
<ul>
 	<li>5 is the application version, which means version 6 will be a complete rebuild</li>
 	<li>1 is the marketing version, which can be used by marketing to announce some new features</li>
 	<li>2 is the minor version, which is used every time there is a new release. This contains for example bug fixes and minor features</li>
 	<li>276 is the build or revision number, which will increment every change in repository or every new build</li>
</ul>
<strong>5. Build automation tool</strong>
The build automation tool is the dashboard of your test automation and the place where all the dots are connected. It basically consists of build steps. Every build step can execute its own separate piece, like running a Powershell script or invoke a checkout of the trunk. The build automation tool keeps track of the status ever build step returns. If all the build steps succeed, there will be a new potentially releasable software package. Examples of build automation tools are <a href="http://ant.apache.org/" target="_blank">Ant</a>, <a href="http://www.phing.info/" target="_blank">Phing</a> (commandline tools), <a href="http://jenkins-ci.org/" target="_blank">Jenkins</a>, <a href="http://www.visualstudio.com/en-us/products/tfs-overview-vs.aspx" target="_blank">Team Foundation Server</a> and <a href="http://www.jetbrains.com/teamcity/" target="_blank">TeamCity</a> (Web based GUI tools).

<strong>6. Create and implement unit tests</strong>
The basis of testing your software is unit testing. This means testing your methods and classes independently. PHPUnit and MSTest are some examples of programs that can execute unit tests and can be invoked from a build automation tool.

<strong>7. Automatically build a test environment</strong>
To make sure your software is working correctly you need a test environment with your latest version of the software. The ingredients used to create such an environment are: compiling and building the latest source code (checked out from the trunk), add external content (like movies or third party tools) and configure the environment (like configuring Windows registry settings). At the end you should have a test-ready environment, build from scratch.

<strong>8. Automatically test your most precious software parts functionally</strong>
Try to find the core of your software and make sure all functionalities are tested on the test environment you created in the previous step. Simulate a user clicking through the application. Software like <a href="http://docs.seleniumhq.org/" target="_blank">Selenium</a> (for web apps) or <a href="http://www.autoitscript.com/site/autoit/" target="_blank">AutoIT Scripting</a> (for Windows apps) can realize this. It’s impossible to test everything, so try to find the most important spots of your app. It is recommended to create a “Happy flow” that follows the path of a user that always chooses the right options. From there on you can expand your tests incrementally as your software evolves. To reduce the build time it is also recommended to functional test in a parallel way.

<strong>9. Optionally create an acceptance environment</strong>
If all functional tests are succeeded, it is possible to create an acceptance environment on-the-fly. This environment can be used for some additional manual testing. For example the Product Owner or QA department can use this to accept or decline a feature.

<strong>10. Optionally create an installation package</strong>
All tests, both unit and functional, are succeeded. This means your software meets most likely the most important quality requirements. At this point it is save to create a software package of the newest release. After the package is created, it is potentially shippable. The choice can be made to do some manual acceptance testing (on the environment created in the previous step).

<strong>11. Create some error reporting</strong>
If a test fails it is important to get feedback, so you can debug the error. By creating a screenshot on error and add an error log and put these on a central place on the network, it will make life much more easier to find the solution for the error. The solutions from step 5 has notification systems to inform the team or individual when a build or test fails or succeeds.

<strong>12. Backup your Build Automation</strong>
Because your build automation ensures the quality of your product, it is promoted to be marked as important as well. That it is why a backup task should be created of the build automation logs and configuration. Also don’t forget to install a virus scanner if you use a build automation tool on a Windows environment.

Hopefully, the twelve steps above will move your software to a whole new level. My experience is that at first CEO’s and CTO’s don’t like the idea above, because it sounds expensive and time consuming. But when bugs are piling up and developers are getting demotivated, there seems to be no way out. Afterwards, those CEO’s and CTO’s cannot imagine to live in a world without build and test automation. Humans make mistakes, so automate your tests and ensure your quality, because quality is the first step to the road of success.

If you have any questions or comment please leave a comment below.
