---
layout: post
title: App-V powershell backup script
authors: [ryan]
comments: true
categories: [App-V]
tags: [Microsoft, App-V, Powershell, Scripting]
image: assets/images/posts/2011-03-08-app-v-powershell-backup-script/app-v-powershell-backup-script-feature-image.png
---
<p style="text-align: justify;">Some applications don't save the settings in the %appdata% directory. This can be a challenge when the application is virtualized. I have an application that save the settings in the installation directory. After repairing the application I lost my personal settings. This is not acceptable so I created a powershell script to backup the application settings. The script creates maximum 5 zip files and add the application settings based on the extension, this can be specific files too. The name of the zip file gets a date and time stamp. When the maximum is reached the oldest file that is created (not edit) is deleted. When the application is closed the script runs so the last settings will be saved. This way there is always a recent backup to restore.

The script has two functions, one for creating the zip file and the second for adding content to the zip file. After the functions the total amount of zip files will be counted and sorted by creation date, this way the oldest zip file can be deleted. The lines highlighted in red can be edited to your application and backup location.</p>

<blockquote><code><span style="color: #99cc00;"># Zip Functions
</span><span style="color: #3366ff;">function </span>Add-Zip
{
param([string]$zipfilename)
</code>
<code> <span style="color: #3366ff;">if</span>(-not (<span style="color: #800080;">test-path</span>($zipfilename)))
{
<span style="color: #800080;">set-content </span>$zipfilename (<span style="color: #c0c0c0;">"PK"</span> + [char]<span style="color: #ff9900;">5</span> + [char]<span style="color: #ff9900;">6</span> + (<span style="color: #c0c0c0;">"$([char]0)"</span> * <span style="color: #ff9900;">18</span>))
(<span style="color: #3366ff;">dir</span>$zipfilename).IsReadOnly = $false
}
</code>
<code>$shellApplication = <span style="color: #800080;">new-object</span> -com shell.application
$zipPackage = $shellApplication.NameSpace($zipfilename)
</code>
<code><span style="color: #3366ff;">foreach</span>($file <span style="color: #3366ff;">in</span> $input)
{
$zipPackage.CopyHere($file.FullName)
<span style="color: #800080;">Start-sleep</span> -milliseconds <span style="color: #ff9900;">500
</span> }
}
</code>
<code><span style="color: #3366ff;">function</span> New-Zip
{
param([string]$zipfilename)
<span style="color: #800080;">set-content</span>$zipfilename (<span style="color: #c0c0c0;">"PK"</span> + [char]<span style="color: #ff9900;">5</span> + [char]<span style="color: #ff9900;">6</span> + (<span style="color: #c0c0c0;">"$([char]0)"</span> * <span style="color: #ff9900;">18</span>))
(<span style="color: #3366ff;">dir</span>$zipfilename).IsReadOnly = $false
}
<span style="color: #99cc00;"># Zip Functions Closed
# $Backup is the backup location include "\"
</span>
$Backup = <span style="color: #ff0000;">"D:\Data\Backup\App\"<span style="color: #000000; font-family: Georgia;">
</span></span></code> <code>$Total = $(<span style="color: #800080;">Get-ChildItem <span style="color: #c0c0c0;">"</span></span><span style="color: #ff0000;"><span style="color: #c0c0c0;">$Backup"</span></span>).count
</code>
<code><span style="color: #3366ff;">While</span> ($Total -gt <span style="color: #ff9900;">5</span>)
{
$Files = <span style="color: #800080;">Get-ChildItem</span> <span style="color: #c0c0c0;">"$Backup</span><span style="color: #ff0000;">*.zip</span><span style="color: #c0c0c0;">"</span> | <span style="color: #800080;">sort-Object</span>-Property CreationTime
<span style="color: #800080;">Remove-Item</span> $Files[<span style="color: #ff9900;">0</span>]
$Total = $(<span style="color: #800080;">Get-ChildItem</span> <span style="color: #c0c0c0;">"$Backup"</span>).count
}
</code>
<code>$time = <span style="color: #800080;">get-date</span>-format dd-MM-yyyy.hh.mm
$name = <span style="color: #c0c0c0;">"<span style="color: #ff0000;">D:\Data\Backup\App\Backup_</span>"</span> + $time +<span style="color: #c0c0c0;">".zip"
</span>$content = <span style="color: #3366ff;">dir</span> <span style="color: #c0c0c0;">"<span style="color: #ff0000;">Q:\APP.001\APP\data\*.extension</span>"
</span></code>
<code>new-zip $name
<span style="color: #3366ff;">dir</span>$content -Recurse | add-Zip $name
</code></blockquote>
<p style="text-align: justify;">To run the powershell script edit the OSD file and add the following lines:</p>

<blockquote><code>&lt;SCRIPT TIMING="POST" EVENT="SHUTDOWN" WAIT="TRUE" PROTECT="TRUE"&gt;
&lt;SCRIPTBODY&gt;Powershell.exe "<span style="color: #ff0000;">D:\Data\Backup\App\Backup.ps1</span>"
&lt;/SCRIPTBODY&gt;
&lt;/SCRIPT&gt;</code></blockquote>
<p style="text-align: justify;">For restoring I don't have a script yet. The way I do it is to open the explorer with the ACDC tool and copy the settings into the application bubble. Click <a title="ACDC at Login Consultants" href="http://www.loginconsultants.com/index.php?option=com_docman&amp;task=doc_details&amp;gid=69&amp;Itemid=149" target="_blank">here</a> to download the ACDC tool (the download is only available after registration). If you have a question or suggestion, please leave a comment.</p>
