---
layout: post
title: 'Hands-on: Immidio Flex+ (VMware UEM)'
authors: [ryan]
comments: true
categories: [UEM]
tags: [Flex, Flex+, Immidio, UEM, User Environment Management]
image: assets/images/posts/2012-09-26-hands-on-immidio-flex/hands-on-immidio-flex-vmware-uem-feature-image.png
---
Immidio released their latest product called Flex+. The new Immidio Flex+ is a User Environment Management product, to manage your complete user environment. Besides Immidio's powerful profile management feature, which they now call Personalization, it is also possible to manage application shortcuts, drive mappings, environment variables and much more.

When I started at Login Consultants I got very experienced with the Immidio products. As a Immidio Certified Technology eXpert I want to share my first experience with Flex+, therefor I created this hands-on post. In this hands-on post I will describe the installation, configuration and new features of Immidio Flex+.

<strong> Installation:</strong>
<a href="{{site.baseurl}}/assets/images/posts/2012-09-26-hands-on-immidio-flex/Immidio_Flex_Custom_Setup.png"><img class="alignleft size-medium wp-image-1738" src="{{site.baseurl}}/assets/images/posts/2012-09-26-hands-on-immidio-flex/Immidio_Flex_Custom_Setup.png" alt="" width="300" height="233" /></a>The installation of Immidio Flex+ is straight forward. When selecting the option "custom", it is possible to only install the management console , Flex Engine, Flex+ Migration or Flex+ Self-Support combined or separately.

The installation directory is not as expected. Immidio has decided to keep the current Flex Profiles directory for upgrading from Immidio Flex Profiles. This way it is possible to easily upgrade from Immidio Flex Profiles to Immidio Flex+. At the next dialog you have to import a license file. Without the license file it is not possible to complete the installation. It is possible to get a free trial license for 30 days for testing purposes.

<strong>Configuration:</strong>

When you start Flex+ for the first time you have to configure the management console. You have to define the Flex Config share first.
<a href="{{site.baseurl}}/assets/images/posts/2012-09-26-hands-on-immidio-flex/Immidio_Flex_Share_Setup.png"><img class="size-medium wp-image-1864 alignright" src="{{site.baseurl}}/assets/images/posts/2012-09-26-hands-on-immidio-flex/Immidio_Flex_Share_Setup.png" alt="" width="300" height="94" /></a>Like Immidio Flex Profiles it is based on a share that will contain the configuration files. Because Flex Profiles is integrated in Flex+ the share will be like the following screenshot:

<a href="{{site.baseurl}}/assets/images/posts/2012-09-26-hands-on-immidio-flex/Immidio_Flex_Share_FlexRepository.png"><img class="alignnone size-medium wp-image-1749" title="" src="{{site.baseurl}}/assets/images/posts/2012-09-26-hands-on-immidio-flex/Immidio_Flex_Share_FlexRepository.png" alt="" width="300" height="168" /></a>

Within the FlexRepository directory all components have their own directory.

<a href="{{site.baseurl}}/assets/images/posts/2012-09-26-hands-on-immidio-flex/Immidio_Flex_Settings_Setup.png"><img class="size-medium wp-image-1865 alignright" src="{{site.baseurl}}/assets/images/posts/2012-09-26-hands-on-immidio-flex/Immidio_Flex_Settings_Setup.png" alt="" width="300" height="295" /></a>

After defining the share you will see this Settings window where you can define the features in Immidio Flex+.

In the tab App-V you can enable the App-V integration. There you have to define the streaming server and specific settings. Therefore a variable can be used to point to the App-V server. For the configuration of the App-V integration you have to read the admin guide (this is still the same as the previous versions of Flex Profiles).

When pressing Ok, Flex+ Server side is ready to be used.

This video will show you how you can configure Flex+ within 10 minutes.

<iframe width="800" height="461" src="http://www.youtube.com/embed/d-j2YePyl0k" frameborder="0"></iframe>

<strong>Client Configuration:</strong>

The configuration of Flex+ is done by Group Policy Management. The Flex+ zip file contains an ADM and ADMx file to configure the client by Group Policy. The following settings are required:

<span style="text-decoration: underline;">Flex Config Files</span> - Central Location of the Flex Config Files.
<span style="text-decoration: underline;">Profile Archives</span> - Location for storing user settings.
<span style="text-decoration: underline;">Run FlexEngine as Group Policy Extension</span> - For running the Flex Engine at logon.
or
A <span style="text-decoration: underline;">Logon script</span> with the following command and parameter:
"Install_Path\FlexEngine.exe" -r

In case of a laptop (possible offline) environment it is possible to use following command and parameter: "Install_Path\FlexEngine.exe" –offlineimport

A <span style="text-decoration: underline;">Logoff Script</span> to store the profile settings.
The following command and parameter is required:
"Install_Path\FlexEngine.exe" -s

<span style="color: #000000;">Because Flex+ does not use a custom shell, it will not hide all the shortcuts. You can use group policy to hide all default Windows and MSI delivered shortcuts.
The applications that will be deployed you have to make a decision in the package default.</span>

<span style="color: #000000;">You can decide to store the shortcuts on a central location, to not create shortcuts from your package or just use the package shortcuts. When you use the default package shortcuts you still have control of the shortcuts because Flex+ can overwrite the shortcuts.</span>

In my opinion it is recommended to not create any shortcut from the package and use Flex+ instead.

<strong>The interface:</strong>

Let's take a look at the new interface. The visual style remains the same as Flex Profiles. The interface has the following tabs.
<blockquote>Personalization
User Environment
Condition Sets
Application Migration</blockquote>
The first tab is the default Flex Profiles now called Personalization. Here you can configure the application data that needs to be roamed. A new feature is conditions, this will be described later in this blog post. I recommend you read the <a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Main.png"><img class="alignright size-medium wp-image-1940" title="Immidio_Flex_Main" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Main.png" alt="" width="300" height="188" /></a>admin guide for the profile solution. The next tab is User Environment, here you can configure and manage the user settings. Conditions Sets can be used to create conditions for specific settings. At Application Migration it is possible to migrate application settings.

<strong>User Environment:</strong>

The following settings can be configured.
<blockquote>Drive Mappings
Environment Variables
File Type Associations
Files and Folders
Logon Task
Logoff Task
Printer Mappings
Registry Settings
Shortcuts
Display Language
Hide Drives</blockquote>
<a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_User_Environment.png"><img class="wp-image-1922 alignright" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_User_Environment.png" alt="" width="143" height="180" /></a>

These are options that are common within User Environment Management products. Flex+ is like Flex Profiles file based. It is a bit different if we compare the UEM part in Flex+ with the profile management. The UEM part uses xml-files instead of ini files. For some settings a zip file is created.
<ul>
 	<li><span style="text-decoration: underline;">General settings:</span></li>
</ul>
A lot of settings are the same in each option. The following settings are asked in each function or are common.

"Name" is the display name of the configured item. This name is used in the management console and does not have any effect on the user. The config xml-file will have the name you specify. It is recommended to use a standard for all naming conventions.

It is optional to add a "label". Labels can be used to organize and order.<a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_General_Settings.png"><img class="alignnone size-medium wp-image-1921" title="" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_General_Settings.png" alt="" width="300" height="58" /></a>

<span style="color: #000000;">"Run once" can be used if the setting only needs to be created once. The setting will be applied once.</span>

"Undo at logoff" is not present in every option, but is common. <a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Common_Settings.png"><img class="size-full wp-image-1920 alignright" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Common_Settings.png" alt="" width="144" height="76" /></a>This option will remove the setting that you configured when the user logs off.

"Run asynchronously" is also not present in every option but common. Use "Run asynchronously" when Flex+ does not have to wait until the action is complete.
<ul>
 	<li><span style="text-decoration: underline;">Creating a Drive Mapping:</span></li>
</ul>
"Remote Path" is the location for the "share to connect". <a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Mappings.png"><img class="alignright size-medium wp-image-1942" title="" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Mappings.png" alt="" width="300" height="79" /></a>

Use the "Friendly Name" to make a clear identification of the share. See the screenshot as an example.

The option "Skip if drive letter is in use" can be very useful if you are not sure a drive letter is already used. The mapping will not be created and the original mapping will used.
<ul>
 	<li><span style="text-decoration: underline;">Create an Environment Variable:</span></li>
</ul>
"Variable name" is the name of the variable. You do not have to use the % character. See the <a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Variable.png"><img class="alignright size-medium wp-image-1943" title="" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Variable.png" alt="" width="300" height="49" /></a>screenshot for an example.

The "Value" is the value of the variable you created.
<ul>
 	<li><span style="text-decoration: underline;">Create a File Type Association:</span></li>
</ul>
"Extension" is the file type extension you want to specify. The extension is include a "." (dot). See the screenshot for example.<a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_FTA.png"><img class="alignright wp-image-1945" title="" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_FTA.png" alt="" width="300" height="115" /></a>

The "Command" is the executable for the application. Do not specify the arguments in the command line. Only use the executable that needs to open the extension.

"Arguments" are the parameter that will open the located file. To specify the selected file, use "%1". Some applications need specific arguments to open files. See the screenshot for an example.

"Icon path" is the location of the icon that is used for the file type. This can be an executable or .icon file. If you use a local icon the icon needs to be located on the machine.

"Icon index" is the number of the icon that is used. To locate the index number "tooling" can be used.

"Description" can be used for extra notes.
<ul>
 	<li><span style="text-decoration: underline;">Create Files and Folders:</span></li>
</ul>
With this option you can pre-create files or <a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Files_and_Folders.png"><img class="size-medium wp-image-1946 alignright" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Files_and_Folders.png" alt="" width="300" height="260" /></a>specific folders that are required for a certain application. When you select "Create" an explorer will open a directory quite similar to a UserProfile. Here you can copy or create the files and folders. When you are finished, Flex+ will only include the created files and folders in a zip file.

&nbsp;
<ul>
 	<li><span style="text-decoration: underline;">Creating Logon and Logoff Tasks:</span></li>
</ul>
Here you can create certain tasks that need to be run at logon or logoff.

At the "Command" you can specify the executable or script that needs to be run.<a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Task.png"><img class="alignright size-medium wp-image-1951" title="" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Task.png" alt="" width="300" height="66" /></a>

Use "Timeout" to specify the total of seconds that the task may use.

"Run task" can be used before or after the profile is imported or exported to run the task.
<ul>
 	<li><span style="text-decoration: underline;">Creating a Printer Mapping:</span></li>
</ul>
Here you can configure the printers that are added by default. It is possible to add every printer on a division when a user is a member of a specific security group. This will be explained in the Conditions part later on.

The "Remote path" is the server and printer location that needs to be mapped.<a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Printer.png"><img class="alignright size-medium wp-image-1950" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Printer.png" alt="" width="300" height="49" /></a>

With "Default printer" you can define when the Pinter is default.
<ul>
 	<li><span style="text-decoration: underline;">Create a Registry Setting:</span></li>
</ul>
Like the option "Files and Folder" you can also create specific registry settings.<a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Reg.png"><img class="alignright size-medium wp-image-1949" title="" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Reg.png" alt="" width="300" height="67" /></a>

With "Create" you get the option to use an empty registry file or import a specific registry file. Flex will automatically add a hive for the Importmarker.
<ul>
 	<li><span style="text-decoration: underline;">Creating a Shortcut:</span></li>
</ul>
The "Shortcut name" is the displayed name that will be used.

Specify the executable in the "Target" area.

In the "Arguments" you can specify any argument when required.<a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Shortcut.png"><img class="alignright size-medium wp-image-1948" title="" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Shortcut.png" alt="" width="300" height="183" /></a>

"Start in" is the location where the executable is started.

You can use the "Comment" for any comments or mental notes.

"Icon path" is the icon that is used. Like the "File Type Association" this can be an executable or icon file.

"Icon index" is the number of the icon that has been used. To locate the index number tooling can be used.

At the "Destination" you can specify where the shortcut needs to be published. Use the "Program folder" to define a location in the start menu.

A great tip from my colleague <a href="http://www.linkedin.com/in/mikebijl" target="_blank">Mike Bijl</a> (<a href="http://blog.compower.org/" target="_blank">Mike's blog</a>) is to use the label to have a clear overview of the start menu structure. You can combine the "Program Folder\Shortcut Name" in the field "Label". By sorting "Label" a decent structure is shown.
<ul>
 	<li><span style="text-decoration: underline;">Using Display Languages:</span></li>
</ul>
Here you can configure the display language that needs to be used. Just create an item and select the language in the pull down menu.<a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Lang.png"><img class="size-medium wp-image-1947 alignnone" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Lang.png" alt="" width="300" height="172" /></a>
<ul>
 	<li><span style="text-decoration: underline;">Hiding Drives:</span></li>
</ul>
To hide specific drives you can use this option. For example it is common to hide the virtual drive of the App-V client.
<a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Hide_Drive.png"><img class="alignnone size-medium wp-image-1952" title="" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Hide_Drive.png" alt="" width="300" height="83" /></a>

<strong>Conditions and Condition sets:</strong>

In every option you can add a condition. A condition is a type of criteria that the system or <a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Condition.png"><img class="alignright size-medium wp-image-1958" title="" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Condition.png" alt="" width="300" height="272" /></a>user needs to be complied with. There are many options to check on. As an example I created a condition for a shortcut. The path of the application needs to be located on the machine and the user needs be a member of the correct active directory group.

When common conditions are used for a lot of items it is also possible to create a condition set. The set can be selected on the specific item, for example a shortcut.

<strong>Application Migration:</strong>

During a migration from one version of an application to another version, you want to keep <a href="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Migrate.png"><img class="alignright size-medium wp-image-1959" title="" src="http://www.logitblog.com/wp-content/uploads/2012/09/Immidio_Flex_Settings_Migrate.png" alt="" width="300" height="145" /></a>most settings of the user that is migrated. It often happens application settings are lost during a migration to a newer operating system. With Flex+ it is possible to migrate application settings to a newer platform. By creating an item you need to specify the source and target personalization item. This way you can save application settings to the new location. It is optionally possible to "Overwrite target profile archive if it already exists".

Detailed information is described in the Flex+ admin guide. You can find the admin guide in the zip file of the Flex+ download.

<strong>Conclusion:</strong>

I think Immidio makes a smart decision to take the step into in the User Environment Management market space. The basic way to configure items is clear and simple. It is very quick to install and no complex database or infrastructure is required. We have to wait and see if Immidio can proof itself in the UEM market. But I think Flex+ is going to be one of the common products out there.

If you have any questions please leave a comment or send Immidio an <a href="mailto:info@immidio.com?subject=Flex+ | Logitblog.com">email</a>.