---
layout: post
title: Running 16bit Dos apps on x64bit OS with App-V
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, Applications, Dos, DOSBox, Sequencing]
image: assets/images/posts/2010-11-16-running-16bit-dos-apps-on-x64bit-os-with-app-v/running-16bit-dos-apps-on-x64bit-os-with-app-v-feature-image.png
---
Since desktops and laptops are now easily equipped with 4GB of RAM there is an increasing of 64bit operation systems. But are your applications supporting 64bits?

I use DOSBox (DOS emulator) to run the DOS applications. You can find more information on the site of <a title="DOSBox" href="http://www.dosbox.com/" target="_blank">DOSBox</a>.

I describe three ways to sequence DOS applications with App-V. The first is to sequence DOSBox and the DOS app in one package. An other way is to sequence DOSBox and the DOS application separated so you can use DOSBox for more DOS applications. And the last one is to sequence DOSBox and run the DOS application from the local machine into the bubble.
<h2>One - Sequence DOSBox and the DOS app in one package.</h2>
This is a short description of an post of my colleague <a title="Linkedin" href="http://nl.linkedin.com/in/rubenkoene/" target="_blank">Ruben Koene </a>at <a title="Softgridblog" href="http://www.softgridblog.com/?p=164" target="_blank">Softgridblog.com</a>.

Zip the DOS application to extract the contend to the local Q drive.

1. Start monitoring and create a directory based on the DOS application name.

2. Install DOSBox on the Q drive in the DOS application directory in a separate directory. (Q:\DOSAPP.001\DOSBox\)
Note: You probably want to uncheck "Create desktop shortcut" because the DOS application will have his own shortcut.

3. Finish the installation of DOSBox.

4. Extract the DOS application into the directory on the Q drive in a separate directory. (Q:\DOSAPP.001\DOSapp\)

5. Open the options of DOSBox in the start menu
(Start menu - All programs - DOSBox 0.74 - Options - DOSBox 0.74 Options)

6. With this action a new config file is created. You can change a lot but, we are only going to add a custom action to execute. Scroll down in the config file and add the following lines.
<blockquote>MOUNT Q Q:\DOSAPP.001\DOSapp\
Q:
APP.exe</blockquote>
7. Save the file as DOSApp.conf in your DOSapp-folder (i.e. Q:\DOSAPP.001\DOSApp\DOSApp.conf)
Note: Use Save As.. or the original will be overridden.

8. Close the config file and stop monitoring.

9. In the Configure Applications-wizard delete all the applications and click the add button. Add the following application path:
<blockquote>“Q:\DOSAPP.001\DOSBox-0.74\DOSBox.exe" –noconsole –conf  “Q:\DOSAPP.001\DOSapp\DOSApp.conf"</blockquote>
10. Add the application icon rename the application name. Click on add and click on next.

11. Launch the application.
Note: Settings are configured in the config file. For more information look <a title="DOSBox.Config" href="http://www.dosbox.com/wiki/Dosbox.conf" target="_blank">here.</a>

12. Finish the deployment wizard and save the sequence.
<h2>Two - Sequences the applications separated</h2>
Zip the DOS application to extract the contend to the local Q drive.

1. Sequence DOSBox the default way. Finish the sequence and make sure DOSBox work properly.

2. Sequence the DOS application. Extract the application files and finish monitoring.

3. Add the executable of the DOS application and try to launch the application.
Note: It doesn't mather if the application gives an error message.

4. Finish the deployment wizard and save the sequence

5. Copy the DOSBox ODS file into the DOSAPP directory.

6. Rename the OSD file into the DOS application name.

7. Open the OSD in your text editor and change the Application name and version into the DOS application name and version.

8. Change PARAMETERS="-userconf" into PARAMETERS="\DOSAPP.001\DOSAPP\APP.EXE -noconsole -exit"
Note: For the parameter of DOSBox look at the site, I used -noconsole to show only the app and -exit to close DOSBox when the app is closed .

9.  Add dynamicsuiting with the DOS application.
<blockquote>&lt;DEPENDENCIES&gt;
&lt;CODEBASE HREF="<a href="file://D:\Apps\VAPPS\TTYCOON.000\TTYCOON.sft">FILE://D:\Apps\VAPPS\DOSAPP.001\DOSAPP.sft</a>" GUID="{GUID}" SYSGUARDFILE="DOSAPP.001\osguard.cp"  MANDATORY="TRUE"/&gt;
&lt;/DEPENDENCIES&gt;</blockquote>
Note: Add your own location of the DOSAPP.sft file.

10. Save the OSD file and deploy the application.
Note: Don't forget to create a desktop shortcut.
<h2>Three - Only sequence DOSBox</h2>
1. Only sequence DOSBox the default way. Finish the sequence and make sure DOSBox work properly.

2. Copy the original OSD file and rename the OSD  file name into the DOS application name.

3. Open the OSD in your text editor and change the Application name and version into the DOS application name and version.

4. Change PARAMETERS="-userconf" into PARAMETERS="D:\Apps\DOSapp\App.exe -noconsole -exit" (Your own application location).

5. Save the OSD file and deploy the application.
Note: Don't forget to create a desktop shortcut.

If you have questions or suggestions leave them in the comments.
