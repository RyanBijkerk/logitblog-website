---
layout: post
hidden: true
title: Automated Citrix Microapp deployment with Azure DevOps
authors: [ryan, eltjo]
categories: [Citrix]
tags: [Azure DevOps, Citrix, Microapp]
image: assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/automated-citrix-microapp-deployment-with-azure-devops-feature-image.png
---
With the fast changing pace of technology, it is important to stay in control and avoid lagging behind in terms of adopting new technology. Nowadays for many modern organizations, drafting up a DevOps focused mindset and strategy is the way to go. Moving away from large and monolithic applications and switching to microapps is another way companies use to get a competitive advantage among the crowd. Using microapps can radically reduce application complexity while increasing user experience. This blog post will show how to use both technologies and teach how to use Azure DevOps to automate Citrix microapp deployment.

## Disclaimer
 > Please note that this blog post is a written version of the "Automating Microapp Deployment with CI/CD in Azure DevOps" presentation for Citrix Converge 2020.

<iframe width="930" height="523" src="https://www.youtube.com/embed/pgpa1iZONyU" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## The value of automation
Cloud is changing our behavior from both a professional and a personal perspective. Each individual is using various applications daily, by phone, television, or at work. A great example is Netflix, as this is available in a lot of households. As a consumer, it is expected from Netflix to be available 24/7 and even though many of the friends and family are also subscribing to the service, it still works as usual. This means Netflix is responsible to scale with the demand. But if the catalog of Netflix stays the same why still bothering having it? Therefore, Netflix has an ever-evolving catalog which is updated continuously, but also the application itself to ensure to deliver all to content in our living room.

Although this might be normal to expect from a service like Netflix, this expectation made its way into the corporate office. Management and employees alike expect the IT department to keep up to par with the latest innovations and in the meantime ensure the services are available 24/7.

**Why automation?** 
Automation empowers organizations to optimize internal resource allocation by capitalizing on opportunity cost and enabled businesses to scale while ensuring consistency in customer experience. With automation, businesses are either saving or making money.

**What to automate?** 
This is business-specific, a great starting point will be repeatable, low-skill processes. These are frequently repetitive tasks that consume a considerable amount of time but on the other hand, are usually easily automatable.

**How to automate?** 
Think big but start small, you cannot afford to do everything in one go and all at once. Ensure to use the technology and services applicable to your level of skillset and organization.

As a consultant, it is clear the demand for automation is ever increasing faster and faster and therefore our skillset needs to accordingly. Many organizations are applying a DevOps mindset and strategy, using tools like Jenkins or Azure DevOps, and are using methods like continuous integration and continuous deployment (CI/CD). Now it not just using a DevOps solution, it is a switch in culture and mindset, and therefore it might be difficult to adopt in some organizations. A fundamental core component, from our perspective, is code. Everything should be treated as code, even documentation. But how can a Citrix microapp be managed by code?

## Citrix Microapp by code
Now one of the best practices of [git](https://git-scm.com/){:target="_blank"}, which is the default versioning system, is don't commit generated files. When exporting an integration including one or more associated microapps from the Citrix Cloud management portal, it will create and download a .mapp file. This file is a compressed archive and therefore it is possible to extract the files contained therein. A microapp consists of the following structure:

```ascii
+-- microapp.mapp
|    +-- i18n
|    |    +-- en.json
|    +-- file.sapp
|    +-- metadata.json
```

The metadata.json contains all the basic information for the microapp. This included information like the title and description but also a list of integrations for the microapp (or associated with the microapp).
<a href="{{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/metadata.json.png" data-lightbox="microapp-metadata.json">
![microapp-metadata.json]({{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/metadata.json.png)
</a>

The file.sapp is a JSON based file that contains all the logic also known as the integrations of the microapp. All the integrations are located in the application section. As shown in the example below, this file is very large and for this relatively simple example, it contains over 2600 lines.
<a href="{{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/files.sapp.collapsed.png" data-lightbox="microapp-files.sapp.collapsed">
![microapp-files.sapp.collapsed]({{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/files.sapp.collapsed.png)
</a>

An 'application' which is the integration will have components. Each component represents a part of the microapp like a text input box or a button. In the example below, it is possible to match this specific line to a text input field for instance.
<a href="{{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/file.sapp.png" data-lightbox="microapp-files.sapp">
![microapp-files.sapp]({{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/file.sapp.png)
</a>

<a href="{{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/microapp-form.png" data-lightbox="microapp-form">
![microapp-form]({{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/microapp-form.png)
</a>

The en.json located in the i18n directory is a language file. All elements of the microapp that contains text, like a button are located here which has a unique identifier. This shows microapps are ready to support multiple languages. Currently, the localization functionality is not used as far as we know.
<a href="{{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/en.json.png" data-lightbox="en.json">
![en.json]({{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/en.json.png)
</a>

## Exporting a microapp
In order to get a microapp translated into code, the microapp needs to be exported. Now this can be done in two ways, using the management interface of Citrix Cloud or via the Citrix API, the latter, of course, being the preferred option.

Using Citrix Cloud there are two possibilities, exporting the integration which includes all microapps or the microapp itself.
<a href="{{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/export-intergration.png" data-lightbox="citrix-export-integration">
![citrix-export-integration]({{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/export-intergration.png)
</a>

This is also possible via the API which is documented [here](https://developer.cloud.com/citrixworkspace/citrix-workspace-platform){:target="_blank"}. 
> Disclaimer, while researching the API it was noticed the metadata.json is empty but is currently not included in the download. We are in contact with Citrix to validate this. The modelConfiguration is still missing in the file.sapp when exporting via the API. 
> 
> This is reported to Citrix.

## Compiling a Microapp and getting it into a pipeline
To get a microapp from code back into Citrix Cloud, the code needs to be compiled into a .mapp file. Now there is no utility available that can achieve this, so therefore a compile script has been created. This script will ensure to collect all the necessary files and compress them into the correct folder structure into a .mapp file.

This method also allows adding validation if the structure is valid or any other additional checks. Now please note this is just a proof of concept at this stage and does require additional work which you can contribute to on GitHub right [here](https://github.com/applicationascode/general){:target="_blank").

The next step is to compile the microapps via a pipeline and publish the artifact. In this sample, the compile script has been included in the git repository.
<a href="{{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/devops-repo.png" data-lightbox="devops-repo">
![devops-repo]({{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/devops-repo.png)
</a>

Following Microsoft Best Practices on Azure DevOps, it’s recommended to create the YAML based pipeline because those are located in the repository and managed by code. The following example is the pipeline that will compile the microapp. This is done in two basics steps. First, it will execute the PowerShell script to generate the .mapp files, and secondly, it will publish the artifact.

```yaml
# Citrix Microapp compile pipeline
trigger:
- master

name: Compile Citrix microapps B$(Rev:rr)

pool:
  vmImage: 'windows-latest'

steps:
- task: PowerShell@2
  displayName: 'Compile Citrix microapps'
  inputs:
    filePath: '$(Build.SourcesDirectory)\scripts\Compile-Microapp.ps1'
    arguments: '-Path "$(Build.SourcesDirectory)\microapps" -DestinationPath "$(Build.ArtifactStagingDirectory)"'

- task: PublishBuildArtifacts@1
  displayName: 'Publish Citrix microapps'
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'microapps'
    publishLocation: 'Container'
```

Please note the PowerShell script is created and dependent on a windows machine so therefore this example is using a host Windows-based build agent in Azure DevOps.

Once the pipeline has run the artifact will be available as shown in the screenshot below.
<a href="{{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/devops-artifact.png" data-lightbox="devops-artifact">
![devops-artifact]({{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/devops-artifact.png)
</a>

## Importing the Microapp into Citrix Cloud
The final step is importing the microapps back into Citrix Cloud. Looking at the flow using the web interface there is a possibility to import the Microapp when creating a new integration. Now due to some security contains, some information was omitted while exporting. This makes sense as you never want to store sensitive information like client key and secret in plain text. So therefore this information needs to amended back into the microapp when importing the microapp shown in the example below.
<a href="{{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/import-integration.png" data-lightbox="import-integration">
![import-integration]({{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/import-integration.png)
</a>

In order to integrate this into a pipeline, the Citrix API needs to be leveraged otherwise it will be very hard to automate. The import bundle method API can be used to import integrations and microapps. Like the web interface, security details need to be provided using the config in the FormData parameter. Unfortunately, there is a bit lack of documentation at this part so it is not clear what the required options are and therefore it was not possible to find the proper way to import the integration at the time of writing, as shown in the screenshot below. Again this was also reported back to the Citrix Microapp team but the documentation hasn’t been updated yet.
<a href="{{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/error-api.png" data-lightbox="error-api">
![error-api]({{site.baseurl}}/assets/images/posts/2021-01-07-automated-citrix-microapp-deployment-with-azure-devops/error-api.png)
</a>

An important note for those who are using the PowerShell, in order to post the FormData PowerShell 6.1.0 or higher is required as this was added to the Invoke-WebRequest commandlet. More information can be found here.

For argument's sake, let’s assume there is a working way to import the microapps using a PowerShell script this can be integrated into a pipeline. In the following example, the artifact will be downloaded and provided to the script to import into Citrix Cloud.

```yaml
# Citrix Microapp release pipeline

trigger:
- master

name: Release Citrix microapps B$(Rev:rr)

pool:
  vmImage: 'windows-latest'

steps:
- task: DownloadBuildArtifacts@0
  inputs:
    buildType: 'specific'
    project: '893571a8-5db1-4eed-8fac-51871bb1073a'
    pipeline: '17'
    buildVersionToDownload: 'latest'
    downloadType: 'single'
    artifactName: 'microapps'
    downloadPath: '$(System.ArtifactsDirectory)'
- task: PowerShell@2
  displayName: 'Release Citrix microapps'
  inputs:
    filePath: '$(Build.SourcesDirectory)\scripts\Release-Microapp.ps1'
    arguments: '-Path "$(Build.ArtifactStagingDirectory)"'
```

## Next step for you and Citrix
This blog post contains a lot of information and concepts that can be applied right away. It is important to believe in code as this will become as this is the core of DevOps strategy.

Store all your code in a git repository as this allows you to properly version the microapps. Ensure the right strategy and best practices are applied then using git, which can be easily be found on the internet.

Prepare for the pipelines in Azure DevOps as this is the way to automate all the steps. Unfortunately for microapps it is not complete yet, but at least compiling can be done. 

For Citrix, it is important to resolve some bugs so everything works are expected. Bugs might occur but it is important these are addressed and fixed.

More APIs are really required to have better manageability over the microapps. For example, there is no update / put functionality at this point. Now as it could not be figured out how to import a microapp using the API is not clear if this will override the existing microapp or add increment like #2 to the name.

There is some documentation missing like the config options for the FormData which is withholding us to successfully use the API. Without proper documentation, an API is basically useless, so Citrix needs to improve this for sure.

When creating a microapp this needs to be done in the Citrix Cloud interface. Now, this is not very applicable in the DevOps strategy so therefore it would be wise to invest in tooling to create microapps like a developer. This like an integration in Visual Studio which allows you to design and develop the microapp which can be directly committed to the git repository.

Hope this blog post was informative and if you have any questions or feedback please leave them in the comments below.

<span>Photo by <a href="https://unsplash.com/@kjarrett?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank">Kevin Jarrett</a> on <a href="https://unsplash.com/?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank">Unsplash</a></span>
