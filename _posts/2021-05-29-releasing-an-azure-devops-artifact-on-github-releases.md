---
layout: post
hidden: false
title: "Releasing an Azure DevOps artifact on GitHub releases"
authors: [ryan]
categories: ['DevOps']
tags: [DevOps, GitHub, PowerShell, Code]
image: assets/images/posts/2021-05-29-releasing-an-azure-devops-artifact-on-github-releases/releasing-an-azure-devops -artifact-on-github-releases-feature-image.png
---
When distributing software there are multiple options to achieve this. It can be provided via the website, using a cloud storage solution, using GitHub, etc. GitHub is a very common location to share the source code and releases, but it can also be used to just release an executable. This blog post will show how to release an Azure DevOps artifact via a GitHub release.

## Releases on GitHub
Before going into the details, it is important to have a clear understanding of releases on GitHub. 

Releases are deployable software iterations you can package and make available for a wider audience to download and use.

Releases are based on Git tags, which mark a specific point in your repository's history. A tag date may be different than a release date since they can be created at different times. For more information about viewing your existing tags, see "Viewing your repository's releases and tags."

You can receive notifications when new releases are published in a repository without receiving notifications about other updates to the repository. For more information, see "Viewing your subscriptions."

Anyone with read access to a repository can view and compare releases, but only people with write permissions to a repository can manage releases. For more information, see "Managing releases in a repository."

People with admin permissions to a repository can choose whether Git Large File Storage (Git LFS) objects are included in the ZIP files and tarballs that GitHub creates for each release. For more information, see "Managing Git LFS objects in archives of your repository."

If a release fixes a security vulnerability, you should publish a security advisory in your repository. GitHub reviews each published security advisory and may use it to send Dependabot alerts to affected repositories. For more information, see "About GitHub Security Advisories."

You can view the Dependents tab of the dependency graph to see which repositories and packages depend on code in your repository, and may therefore be affected by a new release. For more information, see "About the dependency graph."

You can also use the Releases API to gather information, such as the number of times people download a release asset. For more information, see "Releases."

Source: [About releases - GitHub Docs](https://docs.github.com/en/github/administering-a-repository/about-releases){:target="_blank"}

## Creating the artifact
The first step is to create the artifact. This is a bit depending on the type of software you build and create, but the goal is to get the binaries into the artifact staging directory. This can be done using the copy action in the YAML based pipeline.

> Quick side note, it is a best practice to use the YAML based pipelines as these are managed by code in the repository.

``` yaml
    - task: CopyFiles@2
      inputs:
        SourceFolder: '$(System.DefaultWorkingDirectory)\MySolution\bin\$(BuildConfiguration)\'
        Contents: |
          *.exe
          *.dll
        TargetFolder: '$(Build.ArtifactStagingDirectory)'
```
This snipped will copy all executable and DLL files into the ArtifactStagingDirectory.

The next step is to publish the artifact which can be done with the following snippet:
``` YAML
   - task: PublishBuildArtifacts@1
      displayName: 'Publish Artifact: release'
      inputs:
        PathtoPublish: '$(Build.ArtifactStagingDirectory)'
        ArtifactName: 'release'
        publishLocation: 'Container'
```
At this stage, the artifact is available and ready to be released.
## Releasing to GitHub
Now by default Azure DevOps has the GitHubRelease task available, so this means no marketplace plugin needs to be installed to achieve our goal.
But before this can be leveraged, a connection to the GitHub account needs to be made. This can be done in the project settings and the service connections.

<a href="{{site.baseurl}}/assets/images/posts2021-05-29-releasing-a-azure-devops-artifact-on-github-releases/azure-devops-service-connections.png" data-lightbox="azure-devops-service-connections">
![azure-devops-service-connections]({{site.baseurl}}/assets/images/posts/2021-05-29-releasing-a-azure-devops-artifact-on-github-releases/azure-devops-service-connections.png)
</a>

Create a new service connection and select GitHub:

<a href="{{site.baseurl}}/assets/images/posts2021-05-29-releasing-a-azure-devops-artifact-on-github-releases/azure-devops-service-connections-github.png" data-lightbox="azure-devops-service-connections-github">
![azure-devops-service-connections-github]({{site.baseurl}}/assets/images/posts/2021-05-29-releasing-a-azure-devops-artifact-on-github-releases/azure-devops-service-connections-github.png)
</a>

By default the Authentication method is on Grant authorization, this will guide you through the authentication flow. Depending on your preference you can choose one of the two methods.
Hit the authorize and follow the authentication flow.

<a href="{{site.baseurl}}/assets/images/posts2021-05-29-releasing-a-azure-devops-artifact-on-github-releases/azure-devops-service-connections-github-done.png" data-lightbox="azure-devops-service-connections-github-done">
![azure-devops-service-connections-github-done]({{site.baseurl}}/assets/images/posts/2021-05-29-releasing-a-azure-devops-artifact-on-github-releases/azure-devops-service-connections-github-done.png)
</a>

In the end, a service connection is available.

<a href="{{site.baseurl}}/assets/images/posts2021-05-29-releasing-a-azure-devops-artifact-on-github-releases/azure-devops-service-connection-listed.png" data-lightbox="azure-devops-service-connection-listed">
![azure-devops-service-connection-listed]({{site.baseurl}}/assets/images/posts/2021-05-29-releasing-a-azure-devops-artifact-on-github-releases/azure-devops-service-connection-listed.png)
</a>

The GitHubRelease task does require a couple of parameters. At first the service connection and the repository name on GitHub. Secondly, it also required the SHA of the latest commit. As this is a separate repository, this information is not directly available. Luckily GitHub does provide an API so this can be collected using that. Now in this example, PowerShell is used to collect the SHA of the latest commit.

``` YAML
      - task: PowerShell@2
        displayName: 'Get latest SHA commmit from repo'  
        inputs:
          targetType: 'inline'
          script: |
            $commits = Invoke-RestMethod -Method GET -Uri "https://api.github.com/repos/Account/repo/commits"
            $sha = $commits[0].sha
            Write-Host "##vso[task.setvariable variable=sha;]$sha"
```
Now collected this information the release can be created. In this example, the tag is a variable that has been set in a separate task.

``` YAML
      - task: GitHubRelease@1
        inputs:
          gitHubConnection: 'example_github'
          repositoryName: 'Account/repo'
          action: 'create'
          target: '$(sha)'
          tagSource: 'userSpecifiedTag'
          tag: '$(tag)'
          title: 'v$(tag)'
          assets: '$(Build.ArtifactStagingDirectory)\release\*.exe'
          addChangeLog: false
```
In the end, you will end up with a release on GitHub like this:

<a href="{{site.baseurl}}/assets/images/posts2021-05-29-releasing-a-azure-devops-artifact-on-github-releases/gitub-release.png" data-lightbox="gitub-release">
![gitub-release]({{site.baseurl}}/assets/images/posts/2021-05-29-releasing-a-azure-devops-artifact-on-github-releases/gitub-release.png)
</a>
## Staging best practices
A release of the software is in most cases a controlled moment, so you want to avoid on each build a new release is created. In Azure DevOps pipelines you can create multiple stages including conditions. Using the following condition will only run the stage when it is run from the main branch. 

``` YAML
 condition: contains(variables['build.sourceBranch'], 'refs/heads/main') 
```

Please take note, as this stage might be run an a separate build agent, the artifact needs to be download. Here is a full example:

``` YAML
- stage: Release
  condition: contains(variables['build.sourceBranch'], 'refs/heads/main')
  displayName: 'Release Software'  
  jobs:
  - job: Release
    steps:
      - task: DownloadBuildArtifacts@0
        inputs:
          buildType: 'current'
          downloadType: 'single'
          artifactName: 'release'
          downloadPath: '$(System.ArtifactsDirectory)'
```
## Conclusion
In some cases, you might want to release your software on GitHub even though the source code is hosted in Azure DevOps. Leveraging the GitHub integration and the GitHub API it is possible to create a new release via the pipeline. This way you can easily distribute your software.

If you have any questions or feedback on how to improve this, please let me know in the comments.

Photo by <a href="https://unsplash.com/@kellysikkema?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Kelly Sikkema</a> on <a href="https://unsplash.com/s/photos/delivery?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  