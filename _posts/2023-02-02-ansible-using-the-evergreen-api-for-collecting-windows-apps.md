---
layout: post
hidden: false
title: "Ansible: using the Evergreen API for collecting Windows apps"
authors: [ryan]
categories: ['Ansible']
tags: [Ansible, DSC, Code, Evergreen]
image: assets/images/posts/2023-02-02-ansible-using-the-evergreen-api-for-collecting-windows-apps/ansible-using-the-evergreen-api-for-collecting-windows-apps.png
---
Obtaining the latest version of an application can often be a challenge. To address this issue, tools and projects such as Chocolaty, WinGet, or Evergreen were created. However, there may be situations where using these solutions is not permitted, and manual management is required. The Evergreen API enables you to retrieve information on the latest version of an application, while still managing the download and installation process yourself. This blog post will demonstrate an example of using the new Evergreen API in an Ansible playbook.

## What is Evergreen?
[Evergreen](https://stealthpuppy.com/evergreen){:target="_blank"} is a PowerShell module that provides the latest version numbers and download URLs for common Windows applications. The module features simple functions for tasks such as:

Retrieving the latest version of an application for comparison with a previously installed or downloaded version.
Returning the URL for the latest version of the application for local installation or deployment to target machines.

This project is managed by [Aaron Packer](https://twitter.com/stealthpuppy){:target="_blank"}, also known as stealthpuppy, and is available on [GitHub to the public](https://github.com/aaronparker/evergreen){:target="_blank"}.

Recently, Aaron introduced the Evergreen API in a tweet:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">A new version of <a href="https://twitter.com/hashtag/Evergreen?src=hash&amp;ref_src=twsrc%5Etfw">#Evergreen</a> to be pushed to the PowerShell Gallery soon, but more importantly, I&#39;ve marked the Evergreen API as version 1.0.0 - now you can use Evergreen without having to install the module <a href="https://t.co/s2AwXriiZN">https://t.co/s2AwXriiZN</a></p>&mdash; Aaron Parker (@stealthpuppy) <a href="https://twitter.com/stealthpuppy/status/1618559925424357376?ref_src=twsrc%5Etfw">January 26, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

As stated in the tweet, the API allows for the use of Evergreen without installing the necessary PowerShell modules. This is a valuable addition to the project, as it enables individuals like myself to utilize Evergreen while still maintaining control over the installation process.

## Exploring the Evergreen API
The following page contains documentation on how to use the Evergreen API: [How to use the Evergreen API - Evergreen (stealthpuppy.com)](https://stealthpuppy.com/evergreen/invoke/){:target="_blank"}

A Swagger page is also available, enabling you to directly execute the API and view the results within the Swagger interface. In addition to clear documentation, the Swagger interface is useful in gaining a better understanding of the API structure.

[evergreen-api - 1.0.0 - stealthpuppy - SwaggerHub](https://app.swaggerhub.com/apis/stealthpuppy/evergreen-api/1.0.0){:target="_blank"}

There are two basic API calls:
  * /apps, which returns all available applications.
  * /app/{appName}, which returns the details of a specific application.

Both API calls are publicly accessible and do not require any form of authentication.

The schema of an application will always include a version and URI. The URI will contain the download link for the setup file. It is important to note that the other properties may vary among different applications. For example:

{% raw %}
``` json
[
  {
    "Version": "22.01",
    "Architecture": "ARM32",
    "Type": "exe",
    "URI": "https://nchc.dl.sourceforge.net/project/sevenzip/7-Zip/22.01/7z2201-arm.exe"
  }
]

[
  {
    "Version": "22.003.20314",
    "Language": "MUI",
    "Architecture": "x64",
    "Name": "Reader DC 2022.003.20314 MUI for Windows-64bit",
    "URI": "https://ardownload2.adobe.com/pub/adobe/acrobat/win/AcrobatDC/2200320314/AcroRdrDCx642200320314_MUI.exe"
  }
]

```
{% endraw %}

## Using the Evergreen API in an Ansible playbook
The following demonstrates how to utilize the Evergreen API in practice. This example focuses on retrieving the latest version of Microsoft Visual Studio Code (since it's the best editor ever!).

When accessing the information through the Swagger API, it becomes apparent that multiple installers are available. To retrieve the desired version, filtering is necessary based on specific criteria:

  * Architecture: x64
  * Channel: Stable
  * Platform: win32-x64

The output will be the installer for the 64-bit machine. The playbook will follow these steps:

  * Collect the available version
  * Apply filter
  * Install application

The API call can be executed and the required information can be collected by utilizing the win_uri module.

{% raw %}
``` yaml
  - name: "Collect Evergreen info for {{ app_name }}"
    win_uri:
      url: "https://evergreen-api.stealthpuppy.com/app/{{ app_name }}"
      method: GET
      content_type: application/json
      return_content: true
    register: app
```
{% endraw %}

The ```app.json``` return value will be equivalent to the results of the Swagger API, which is a list of available installers.

A json_query filter can be applied to obtain the correct version:

{% raw %}
``` yaml
  - name: "Set Evergreen object for {{ app_name }}"
    set_fact:
      app_details: "{{ app.json | json_query('[?Architecture == `x64` && Channel == `Stable` && Platform == `win32-x64`]') | first }}"
```
{% endraw %}

By selecting the first result, only one property will be returned, ensuring that app_details is a single object rather than a list.

Based on the details, the application can be installed:
{% raw %}
``` yaml
  - name: "Install {{ app_name }} {{ app_details.Version }}"
    win_package:
      path: "{{ app_details.URI }}"
      creates_path: C:\Program Files\Microsoft VS Code\Code.exe
      arguments: /VERYSILENT /NORESTART /MERGETASKS=!runcode
      expected_return_code: [0]
      state: present
```
{% endraw %}

The application can then be installed based on the details. The win_package path can be a URL, allowing for direct download of the setup from the internet. It is important to note that an internet connection is required for both using the API and downloading the setup.
Example playbook:

{% raw %}
``` yaml
---
- name: Test Evergreen
  hosts: mngt
  vars_files:
  - ansible.yml
  - domain.yml
  tasks:

  - name: Update fact to MicrosoftVisualStudioCode
    set_fact:
      app_name: MicrosoftVisualStudioCode

  - name: "Collect Evergreen info for {{ app_name }}"
    win_uri:
      url: "https://evergreen-api.stealthpuppy.com/app/{{ app_name }}"
      method: GET
      content_type: application/json
      return_content: true
    register: app

  - name: "Set Evergreen object for {{ app_name }}"
    set_fact:
      app_details: "{{ app.json | json_query('[?Architecture == `x64` && Channel == `Stable` && Platform == `win32-x64`]') | first }}"

  - name: "Install {{ app_name }} {{ app_details.Version }}"
    win_package:
      path: "{{ app_details.URI }}"
      creates_path: C:\Program Files\Microsoft VS Code\Code.exe
      arguments: /VERYSILENT /NORESTART /MERGETASKS=!runcode
      expected_return_code: [0]
      state: present
```
{% endraw %}

## Conclusion
Evergreen was already a robust project, but with the addition of the Evergreen API, it can be consumed in even more versatile ways. This blog post demonstrated an example of integrating the API in an Ansible playbook, showcasing its benefits.

However, there are a few areas for improvement. The "app" object returns various properties that vary between applications. Consistency across all packages would be more advantageous, as it would allow for the use of a single filter, for example. At the time of writing, there is also no information in the API regarding the required installation parameters. Including this information in the API would be valuable, so you don't have to search for it elsewhere in the project.

Despite these areas for improvement, I am truly impressed with the Evergreen API. I was able to create this example from scratch within 15 minutes. Keep up the great work, Aaron, and I look forward to following the progress of your project.

Photo by [fabio](https://unsplash.com/@fabioha?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"} on [Unsplash](https://unsplash.com/photos/oyXis2kALVg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"}
