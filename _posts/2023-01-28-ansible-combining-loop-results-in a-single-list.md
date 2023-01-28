---
layout: post
hidden: false
title: "Ansible: combining loop results in a single list"
authors: [ryan]
categories: ['Ansible']
tags: [Ansible, DSC, Code]
image: assets/images/posts/2023-01-28-ansible-combining-loop-results-in a-single-list/ansible-combining-loop-results-in a-single-list-feature-image.png
---
Sometimes, you may encounter a scenario that appears to be straightforward, but when it comes to coding, you find yourself quickly becoming frustrated. One such challenge is combining the results of multiple loops into a single list. It can take some time to find the right solution, and I thought it would be useful to document the process. This blog post will explain the challenge in detail and provide a solution for combining the results of multiple loops into a single list.

## The challenge
The goal of this challenge is to collect data from multiple locations and combine it into a single list for further processing. This example will focus on collecting information on a Windows-based system, but remember that the solution can be applied to both Windows and Linux systems.

{% raw %}
``` yaml
- name: Test on the management server
  hosts: mngt
  vars_files:
  - ansible.yml

  tasks: 

  - name: collect dirs
    win_find:
      paths: "{{ item }}"
      patterns: ['*.exe']
    register: find
    loop:
      - C:\Program Files (x86)\LoadGen\LoadGen Agent
      - C:\Program Files\Microsoft VS Code

  - name: show results
    debug:
      msg: "{{ find }}"

```
{% endraw %}
The variable 'find' will contain the following information. Note that for improved readability, some objects have been removed from the output.

{% raw %}
```yaml
ok: [mngt-1] => {
    "msg": {
        "changed": false,
        "msg": "All items completed",
        "results": [
            {
                "ansible_loop_var": "item",
                "files": [
                    {

                        "path": "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent\\DNKLocalClient.exe"
                    },
                    {
                        "path": "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent\\DNKRDExec.exe"
                    },
                    {
                        "path": "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent\\DNKVMwareView.exe"
                    },
                    {
                        "path": "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent\\DNKWVD.exe"
                    },
                    {
                        "path": "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent\\DNKXenAppExec.exe"
                    },
                    {
                        "path": "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent\\DNKXenAppExecEnhanced.exe"
                    },
                    {
                        "path": "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent\\LoadGen Firewall.exe"
                    },
                    {
                        "path": "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent\\LoadGen LoadBot Agent.exe"
                    },
                    {
                        "path": "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent\\TestLoadBot.exe"
                    }
                ],
                "invocation": {
                    "module_args": {
                        "age": null,
                        "age_stamp": "mtime",
                        "checksum_algorithm": "sha1",
                        "file_type": "file",
                        "follow": false,
                        "get_checksum": true,
                        "hidden": false,
                        "paths": [
                            "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent"
                        ],
                        "patterns": [
                            "*.exe"
                        ],
                        "recurse": false,
                        "size": null,
                        "use_regex": false
                    }
                },
                "item": "C:\\Program Files (x86)\\LoadGen\\LoadGen Agent",
                "matched": 9
            },
            {
                "ansible_loop_var": "item",
                "changed": false,
                "examined": 23,
                "failed": false,
                "files": [
                    {
                        "path": "C:\\Program Files\\Microsoft VS Code\\Code.exe"
                    },
                    {
                        "path": "C:\\Program Files\\Microsoft VS Code\\unins000.exe"
                    }
                ],
                "invocation": {
                    "module_args": {
                        "age": null,
                        "age_stamp": "mtime",
                        "checksum_algorithm": "sha1",
                        "file_type": "file",
                        "follow": false,
                        "get_checksum": true,
                        "hidden": false,
                        "paths": [
                            "C:\\Program Files\\Microsoft VS Code"
                        ],
                        "patterns": [
                            "*.exe"
                        ],
                        "recurse": false,
                        "size": null,
                        "use_regex": false
                    }
                },
                "item": "C:\\Program Files\\Microsoft VS Code",
                "matched": 2
            }
        ],
        "skipped": false
    }
}

```
{% endraw %}

The output displays an object with a "results" value, which is an array containing multiple objects. In this example, there are two result objects as the task was executed using two loop paths. Each result object has a property called "files", which is also an array containing strings of the resulting files. In this example, these are the executables that were found at the designated locations. The goal is to obtain a list of the files from both results.

## The solution
To achieve the desired outcome, the json_query function can be utilized to filter the results. The function allows for the application of [JMESPath](https://jmespath.org/){:target="_blank"} filters. To gain a better understanding of how to apply these filters, it is recommended to read the documentation, which includes multiple examples that can be experimented with on the website.

In this specific case, the following filter will yield the desired result: ```results[*].files[*].path```.
However, when using this filter with the json_query function, the following result will be obtained:

{% raw %}
```yaml
{
            [
                "C:\\Program Files\\VMware\\VMware Tools\\7za.exe",
                "C:\\Program Files\\VMware\\VMware Tools\\cblauncher.exe",
                "C:\\Program Files\\VMware\\VMware Tools\\rpctool.exe",
                "C:\\Program Files\\VMware\\VMware Tools\\vmtoolsd.exe",
                "C:\\Program Files\\VMware\\VMware Tools\\VMToolsHookProc.exe",
                "C:\\Program Files\\VMware\\VMware Tools\\VMwareNamespaceCmd.exe",
                "C:\\Program Files\\VMware\\VMware Tools\\VMwareResolutionSet.exe",
                "C:\\Program Files\\VMware\\VMware Tools\\VMwareToolboxCmd.exe",
                "C:\\Program Files\\VMware\\VMware Tools\\VMwareXferlogs.exe"
            ],
            [
                "C:\\Program Files\\Windows Media Player\\setup_wm.exe",
                "C:\\Program Files\\Windows Media Player\\wmlaunch.exe",
                "C:\\Program Files\\Windows Media Player\\wmpconfig.exe",
                "C:\\Program Files\\Windows Media Player\\wmplayer.exe",
                "C:\\Program Files\\Windows Media Player\\wmpnetwk.exe",
                "C:\\Program Files\\Windows Media Player\\wmpnscfg.exe",
                "C:\\Program Files\\Windows Media Player\\wmprph.exe",
                "C:\\Program Files\\Windows Media Player\\wmpshare.exe"
            ]
}
```
{% endraw %}

As there are two result objects, the paths are split into two separate arrays, which is not the desired outcome. To combine these two arrays into a single one, the "pipe flatten" can be used. The following command will give the desired end result:

{% raw %}
```yaml
"{{ find | json_query('results[*].files[*].path') | flatten }}"
```
{% endraw %}

Result:
{% raw %}
```yaml
{
    [
        "C:\\Program Files\\VMware\\VMware Tools\\7za.exe",
        "C:\\Program Files\\VMware\\VMware Tools\\cblauncher.exe",
        "C:\\Program Files\\VMware\\VMware Tools\\rpctool.exe",
        "C:\\Program Files\\VMware\\VMware Tools\\vmtoolsd.exe",
        "C:\\Program Files\\VMware\\VMware Tools\\VMToolsHookProc.exe",
        "C:\\Program Files\\VMware\\VMware Tools\\VMwareNamespaceCmd.exe",
        "C:\\Program Files\\VMware\\VMware Tools\\VMwareResolutionSet.exe",
        "C:\\Program Files\\VMware\\VMware Tools\\VMwareToolboxCmd.exe",
        "C:\\Program Files\\VMware\\VMware Tools\\VMwareXferlogs.exe",
        "C:\\Program Files\\Windows Media Player\\setup_wm.exe",
        "C:\\Program Files\\Windows Media Player\\wmlaunch.exe",
        "C:\\Program Files\\Windows Media Player\\wmpconfig.exe",
        "C:\\Program Files\\Windows Media Player\\wmplayer.exe",
        "C:\\Program Files\\Windows Media Player\\wmpnetwk.exe",
        "C:\\Program Files\\Windows Media Player\\wmpnscfg.exe",
        "C:\\Program Files\\Windows Media Player\\wmprph.exe",
        "C:\\Program Files\\Windows Media Player\\wmpshare.exe"
    ]
}
```
{% endraw %}

Full playbook:

{% raw %}
```yaml
- name: Test on the management server
  hosts: mngt
  vars_files:
  - ansible.yml

  tasks: 

  - name: Collect dirs from multiple locations
    win_find:
      paths: "{{ item }}"
      patterns: ['*.exe']
    register: find
    loop:
      - C:\Program Files\VMware\VMware Tools
      - C:\Program Files\Windows Media Player

  - name: Get list with output
    debug:
      msg: "{{ find | json_query('results[*].files[*].path') | flatten }}"

```
{% endraw %}

## Conclusion
When one is not entirely familiar with a solution, as is the case with Ansible for me, it can take some time to understand how to achieve the desired result. This example, covered in this blog post, was the case for me. 

By using the json_query function, it is possible to achieve the desired solution. It is important to have an understanding of the desired outcome structure in order to create the appropriate JMESPath filter. Additionally, it is important to learn the power of [JMESPath](https://jmespath.org/){:target="_blank"} filters, as it also allows for filtering on specific objects within the query. 

I hope this blog post was useful and if you have any questions, please feel free to leave a comment below.

Photo by <a href="https://unsplash.com/@glenncarstenspeters?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText" target="_blank">Glenn Carstens-Peters</a> on <a href="https://unsplash.com/photos/RLw-UC03Gwc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText" target="_blank">Unsplash</a>
