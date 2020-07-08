---
layout: post
title: Login VSI workload customization
date: 2013-10-24 20:07
author: Ryan
comments: true
categories: [Login VSI]
tags: [Customization, eDrawing, Login VSI, Workload]
image: assets/images/posts/2013-10-24-login-vsi-workload-customization/login-vsi-workload-customization-feature-image.png
---
Login VSI supports workload customization, this way you can add your own applications to a workload to generate load on your environment. But how does workload customization work? In this blog post I will describe how to start and how to create a custom workload.
<blockquote><strong>Note</strong>: Workload customization is only available in Login VSI Pro. For more information please visit the <a href="http://www.loginvsi.com" target="_blank">Login VSI</a> website.</blockquote>
<h2><!--more--></h2>
<blockquote><strong>Please note:</strong> This is a personal blog and  not an official Login VSI blog post.</blockquote>
<h2></h2>
<h2>Define your goal</h2>
What do you want to achieve with the workload customization? There are different scenario's, you can add an application to the workload and measure the impact compared to workload without the application. This way you can guarantee you have enough recourses to run the application on a large scale. It is also possible to measure the application start time and if possible improve the start time. Another case is measuring the impact on the back-end, do I have enough recourses on the back-end to deliver the information? Define what you want to achieve with workload customization, this can help you with creating the workload. In this blog post my goal is to measure the impact of an application and collect the application start time.
<h2>The average user</h2>
What does the average user do in the application you are going to add to the workload? That is a hard question but let's start simple. In this blog post I will use <a href="http://www.edrawingsviewer.com/ed/edrawings-viewer.htm" target="_blank">SolidWorks eDrawings</a> as an example. My example users are using the application to review drawings that are created by another department. They will open a drawing rotate the 3D drawing, disassemble and finally close the application. Please remember, you can always extend the workload actions, so start simple.
<h2>Prepare segment</h2>
At first we need to make sure there are no unexpected pop-ups. With new users the application can provide messages about settings, auto updates or just help tips. If these pop-ups are always shown you can create this within the workload otherwise you need to prevent this.

We are going to prevent <a href="{{site.baseurl}}/assets/images/posts/2013-10-24-login-vsi-workload-customization/eDrawing-registration.png"><img class="size-medium wp-image-2606 alignright" src="{{site.baseurl}}/assets/images/posts/2013-10-24-login-vsi-workload-customization/eDrawing-registration.png" alt="" width="300" height="199" /></a>the registration pop-up. You can use tools like <a href="http://sourceforge.net/projects/regshot/" target="_blank">Regshot </a>to detect which changes are made when you disable the pop-up. So in my example I need a registry value to be created to prevent the registration pop-up.

&nbsp;

Within the prepare segment we will create a new VSI_RegWrite which will create the required registry value. It is recommended to add a comment so you remember why it is added.
<pre class="brush: text; gutter: false">VSI_RegWrite("Workload", "HKEY_CURRENT_USER\Software\eDrawings\e2014\General", "Activated", "REG_DWORD", "1")</pre>
<blockquote><strong>Note: </strong>The Login VSI workload language reference language documentation can be found <a href="http://www.loginvsi.com/pdf/documentation/Login-VSI-40-Workload-Language-Reference-Guide.pdf" target="_blank">here</a>.</blockquote>
<h2></h2>
<h2>Creating the library</h2>
eDrawing uses the file type association .easm. When we open an .easm file the drawing is opened directly. To get the file on the target machine we are going to create a library which contains .easm files. The example file can be found <a href="http://www.edrawingsviewer.com/ed/edrawings-samples.htm" target="_blank">here</a>.

Within the {VSIshare}\_VSI_Content\ <img class="alignright size-medium wp-image-2608" src="{{site.baseurl}}/assets/images/posts/2013-10-24-login-vsi-workload-customization/Content-lib-easm-files.png" alt="" width="300" height="140" />directory we can create a new folder called easm. Here we are storing the files and rename those with numbers as seen in the screenshot.

&nbsp;

The next step is to add the .easm file type association in the datalocation.ini. The .ini is located in the {VSIshare}\_VSI_Configuration\_CurrentTest\ directory. Create under the [Library] section a new entry as followed:
<pre class="brush: text; gutter: false">easm=&lt;amount&gt;</pre>
<img class="alignnone size-medium wp-image-2609" src="{{site.baseurl}}/assets/images/posts/2013-10-24-login-vsi-workload-customization/Datalocation-easm-added.png" alt="" width="300" height="205" />

At the above screenshot we have defined 5 .eams files located on the VSIshare that we can use within the workload.
<h2>Creating the workload</h2>
So we are going to use the file type association to open the workload file. First we need to be sure the file is copied from the VSIshare to the target machine. For this action we can use the VSI_Random_File_Copy command which will copy a random file from the library we created earlier.
<pre class="brush: text; gutter: false">VSI_Random_File_Copy("eDraw1", "EASM", "%VSI_Userhome%\EASM1.easm")</pre>
Now that we have the file we can open the application with the following command.
<pre class="brush: text; gutter: false">App_Start("eDraw1", "", "%VSI_Userhome%\EASM1.easm", "Title", "eDrawings")</pre>
We need to make sure the application is started and active, this can be done by a App_Focus.
<pre class="brush: text; gutter: false">App_Focus("eDraw1", "Title", "eDrawings", "", "Maximize")</pre>
when we are sure the application is open we can send the keystrokes to rotate the drawing. Earlier on we have learned the example user is rotating and disassemble the drawing. We can achieve this by using the arrows keys and "e" to disassemble. We are adding this with the following commands:
<pre class="brush: text; gutter: false">VSI_Type_Fixed("eDraw1", "{left}{left}{left}{left}{left}{left}")
VSI_Type_Fixed("eDraw1", "{down}{down}{down}{down}{down}{down}")
VSI_Type_Fixed("eDraw1", "{left}{left}{left}{left}{left}{left}")
VSI_Type_Fixed("eDraw1", "{down}{down}{down}{down}{down}{down}")
VSI_Type_Fixed("eDraw1", "e")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
VSI_Type_Fixed("eDraw1", "e")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")</pre>
Finally we are going to close the application with the App_Close command.
<pre class="brush: text; gutter: false">App_Close("eDraw1", "Title", "eDrawings")</pre>
The next step will be testing this on a target machine. It is recommended to save this in a separate workload before combining this with an existing workload. This way we are sure the custom workload is working properly. Now it is possible to combine it with an existing workload.

<iframe width="800" height="461" src="https://www.youtube.com/embed/zo3HsKZyTrs" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Complete eDrawing workload:
<pre class="brush: text; gutter: false">#WLProp:Version#1.0.0
#WLProp:Date#2013-10-11
#WLProp:Description#The Logitblog eDrawing Workload
##### Start segment Prepare
Segment("Workload", "Prepare")

# Skip eDrawing registration screen
VSI_RegWrite("Workload", "HKEY_CURRENT_USER\Software\eDrawings\e2014\General", "Activated", "REG_DWORD", "1")

# Random wait before starting workload
Workload_RandomIdle("Workload", 5, %VSI_RandomInitialWait%, "Waiting to start workload")

# Set mouse position
VSI_Mouse_Position("Workload")

##############################################################
##### Start segment 1 - eDrawing                         #####
##############################################################
Segment("Workload", 1)

VSI_Random_File_Copy("eDraw1", "EASM", "%VSI_Userhome%\EASM1.easm")
App_Start("eDraw1", "", "%VSI_Userhome%\EASM1.easm", "Title", "eDrawings")
App_Focus("eDraw1", "Title", "eDrawings", "", "Maximize")
VSI_Type_Fixed("eDraw1", "{left}{left}{left}{left}{left}{left}")
VSI_Type_Fixed("eDraw1", "{down}{down}{down}{down}{down}{down}")
VSI_Type_Fixed("eDraw1", "{left}{left}{left}{left}{left}{left}")
VSI_Type_Fixed("eDraw1", "{down}{down}{down}{down}{down}{down}")
VSI_Type_Fixed("eDraw1", "e")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
VSI_Type_Fixed("eDraw1", "e")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
VSI_Type_Fixed("eDraw1", "{up}{up}{up}{up}{up}{up}{up}{up}{up}")
VSI_Type_Fixed("eDraw1", "{right}{right}{right}{right}{right}")
App_Close("eDraw1", "Title", "eDrawings")</pre>
<blockquote><strong>Note:</strong> For the VSImax measurements the VSI_Timer() command needs to be added to the workload.</blockquote>
<h2></h2>
<h2>Conclusion</h2>
The Login VSI meta language lets you easily add custom applications to the workloads. Before starting there are some things to remember.
<ul>
 	<li>A Login VSI PRO license is required for workload customization. More info <a href="http://www.loginvsi.com/licenses" target="_blank">here.</a></li>
 	<li>Define your goal, what do you want to achieve with the customization?</li>
 	<li>Start with basic actions, you can always extend the workload.</li>
 	<li>Test the specific application before adding it to an existing workload.</li>
</ul>
In an upcoming blog post I will analyze the results of a custom workload. If you want to get notified just subscribe here:
[jetpack_subscription_form]

I hope this blog post was helpful and if you have any questions or comments please contact me by leaving a comment below.
