---
layout: post
title: Owner permissions on Mandatory profile with Windows 10
authors: [ryan]
comments: true
categories: [Windows]
tags: [Mandatory Profile, Powershell, Windows 10, Windows 10]
image: assets/images/posts/2017-12-22-owner-permissions-on-mandatory-profile-with-windows-10/owner-premissions-on-mandatory-profile-windows-10-feature-image.png
---
It is common to use mandatory profiles on managed devices like Thin Clients, Fat Client or a VDI environment. During a deployment, I ran into the issue the "User profile cannot be loaded" while all security permissions are correct. This may because of the Owner and in this blog post, I will share how to solve this issue.
<h2><!--more--></h2>
<h2>User profile cannot be loaded</h2>
When a mandatory profile is not properly configured you may experience the following error.
<blockquote>The User Profile Service service failed the sign-in.
User profile cannot be loaded.</blockquote>
<a href="{{site.baseurl}}/assets/images/posts/2017-12-22-owner-permissions-on-mandatory-profile-with-windows-10/user-profile-cannot-be-loaded.png"><img class="alignnone size-medium wp-image-4454" src="{{site.baseurl}}/assets/images/posts/2017-12-22-owner-permissions-on-mandatory-profile-with-windows-10/user-profile-cannot-be-loaded.png" alt="" width="300" height="194" /></a>

Within the event log, the following error with Event ID 1526 will be reported.
<blockquote>Windows could not load your roaming profile and is attempting to log you on with your local profile. Changes to the profile will not be copied to the server when you log off. Windows could not load your profile because a server copy of the profile folder already exists that does not have the correct security. Either the current user or the Administrators group must be the owner of the folder.</blockquote>
As described in the error the Administrators group must be the owner of the folder otherwise the profile cannot be loaded.
<h2>Setting the correct owner</h2>
Within the security tab, the owner can be set. Go to the properties of the mandatory profile, open the security tab and click advanced.

<a href="{{site.baseurl}}/assets/images/posts/2017-12-22-owner-permissions-on-mandatory-profile-with-windows-10/advanced-security-settings-system.png"><img class="alignnone size-medium wp-image-4453" src="{{site.baseurl}}/assets/images/posts/2017-12-22-owner-permissions-on-mandatory-profile-with-windows-10/advanced-security-settings-system.png" alt="" width="300" height="203" /></a>

Click on change and make sure to switch the location to the local machine.

<a href="{{site.baseurl}}/assets/images/posts/2017-12-22-owner-permissions-on-mandatory-profile-with-windows-10/advanced-security-settings-administrators.png"><img class="alignnone size-medium wp-image-4452" src="{{site.baseurl}}/assets/images/posts/2017-12-22-owner-permissions-on-mandatory-profile-with-windows-10/advanced-security-settings-administrators.png" alt="" width="300" height="203" /></a>

This can also be done with PowerShell which may be useful in a deployment scenario.
<pre class="lang:ps decode:true ">$profilePath = "C:\Users\ManProfile.v6"
$account = New-Object -TypeName System.Security.Principal.NTAccount -ArgumentList "BUILTIN\Administrators"
$acl = Get-Acl -Path $profilePath
$acl.SetOwner($account)
Set-Acl -Path $profilePath -AclObject $acl</pre>
<h2>Mandatory profile permissions</h2>
It may be useful to have some basic information on the required permissions needed on the mandatory profile. The following table shows which permissions are required in a mandatory profile. Please note the permissions should also be set on registry level in the NTUSER.MAN.
<table>
<tbody>
<tr>
<td width="500"><strong>User / Group</strong></td>
<td width="150"><strong>Permissions</strong></td>
</tr>
<tr>
<td width="500">NT AUTHORITY\ALL APPLICATION PACKAGE</td>
<td width="150">Full Control</td>
</tr>
<tr>
<td width="500">NT AUTHORITY\SYSTEM</td>
<td width="150">Full Control</td>
</tr>
<tr>
<td width="500">BUILTIN\Administrators</td>
<td width="150">Full Control</td>
</tr>
<tr>
<td width="500">NT AUTHORITY\Authenticated Users</td>
<td width="150">Read and Execute</td>
</tr>
</tbody>
</table>

In my scenario, I have decided to copy the mandatory profile on the local device for performance reasons. This way the copy action will not take place over the network which may be an issue in branch offices. When copying the mandatory profile during the deployment the permissions are inherited from the folder structure. Using the following script will block the inheritance and sets the correct required permissions described in the table above.

<pre class="lang:ps decode:true">$accessRules = @()
$accessObj = New-Object PsObject
$accessObj | Add-Member NoteProperty Name "NT AUTHORITY\SYSTEM"
$accessObj | Add-Member NoteProperty Permission "FullControl"
$accessRules += $accessObj

$accessObj = New-Object PsObject
$accessObj | Add-Member NoteProperty Name "NT AUTHORITY\ALL APPLICATION PACKAGES"
$accessObj| Add-Member NoteProperty Permission "FullControl"
$accessRules += $accessObj

$accessObj = New-Object PsObject
$accessObj | Add-Member NoteProperty Name "BUILTIN\Administrators"
$accessObj| Add-Member NoteProperty Permission "FullControl"
$accessRules += $accessObj

$accessObj = New-Object PsObject
$accessObj | Add-Member NoteProperty Name "NT AUTHORITY\Authenticated Users"
$accessObj| Add-Member NoteProperty Permission "ReadAndExecute"
$accessRules += $accessObj

# Set permission for each access rule
foreach ($accessRule in $accessRules ) {
	
	$acl = Get-Acl -Path $profilePath
	$aclObj = New-Object System.Security.AccessControl.FileSystemAccessRule($accessRule.Name, $accessRule.Permission, "ContainerInherit,ObjectInherit", "None","Allow")
	$acl.AddAccessRule($aclObj)
	Set-Acl -AclObject $acl -Path $profilePath
	}
</pre>

<h2>Conclusion</h2>
If you experience any issue with your mandatory profile the cause may be related to permissions. Of course, the first step is to check the event log for any related errors. By using PowerShell you can make sure the permissions are always set correctly so you don't have to worry about that! If you any comments or question please leave them below!

<span style="text-decoration: underline;">Photo by <a href="https://unsplash.com/photos/2LowviVHZ-E?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Ben Sweet</a> on <a href="https://unsplash.com/?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>
