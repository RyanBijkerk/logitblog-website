---
layout: post
hidden: true
title: "Mastering Mocking with Pester: CommandNotFoundException"
authors: [ryan]
categories: ['PowerShell']
tags: [PowerShell, Pester, Mock, Unit testing]
image: assets/images/posts/2023-08-18-mastering-mocking-with-pester-commandnotfoundexception/mastering-mocking-with-pester-commandnotfoundexception-feature-image.png
---
Unit testing serves as a crucial method to ensure the quality of code. This principle is just as applicable to PowerShell through the utilization of Pester. Pester, the ubiquitous testing and mocking framework for PowerShell, becomes an indispensable tool in this context. However, what should one do when you encounter CommandNotFoundException? In this blog post, we'll explore this specific scenario and explain the resolution within your Pester tests.
## Grasping Mocking in Pester
For those unfamiliar with Pester and its mocking capabilities, here's a concise introduction to Pester's mocking features. As outlined in the documentation, Pester offers a set of mocking functions that streamline the process of simulating dependencies and validating behaviors. These functions enable you to effectively "shim" data layers or mock intricate functions that already possess their own tests.

Source: [Mock](https://pester.dev/docs/commands/Mock){:target="_blank"}

When conducting code tests, the objective is to validate the logic of a script or function without actually executing it. Imagine a scenario where a script is designed to delete user accounts from Azure Active Directory. Naturally, during testing, you wouldn't want actual deletions to occur. Mocking a commandlet replaces its genuine execution during tests, granting you the ability to manipulate behaviors and create diverse test scenarios.

To illustrate, consider the following mock commandlet:

```PowerShell
Mock Get-Content {"This is a mocked example"}
```

In this example, any invocation of `Get-Content` during test execution returns the string "This is a mocked example."
## The Challenge and Its Solution

A prerequisite when mocking commandlets or functions is the availability of the actual commandlet. Let's revisit the previous example. I've constructed a simple script that removes an Azure AD User:

```PowerShell
[CmdletBinding()]
param (
    [Parameter()]
    [string]
    $UserEmail
)

Get-AzureADUser -ObjectId $UserEmail | Remove-AzureAdUser -Force
```

To test this, I've created a concise Pester test:

```PowerShell
Describe "Azure script" {

    Context "When parameters are correct" {

        It "Should remove the Azure AD User" {

            Mock Get-AzureADUser {}
            Mock Remove-AzureAdUser {}
            { .\Azure.ps1 -UserEmail "ryan@go.euc"} | Should -Not -Throw

        }
    }
}
```

Running this test on a machine without the AzureAD module yields the following error:

```PowerShell
Starting discovery in 1 files.
Discovery found 1 tests in 71ms.
Running tests.
[-] Azure script.When parameters are correct.Should remove the Azure AD User 30ms (22ms|8ms)
 CommandNotFoundException: Could not find Command Get-AzureADUser
 
Tests completed in 198ms
Tests Passed: 0, Failed: 1, Skipped: 0 NotRun: 0
```

To address this, the `Get-AzureAdUser` and `Remove-AzureAdUser` commands need to be available. Two approaches can achieve this.

The simpler approach involves installing and loading the module in the testing environment. However, circumstances may occur where installation is unfeasible or, as in this example, loading the module isn't possible. Personally, I work on a Macbook Pro M2, and attempting to load the module leads to the following error:

```PowerShell
Import-Module: The current processor architecture is: MSIL. The module '/Users/ryan/.local/share/powershell/Modules/AzureAD/2.0.2.182/AzureAD.psd1' requires the following architecture: Amd64.
```

To bypass this, dummy functions can be crafted and defined in the `BeforeAll` block. This approach makes the commandlet available, enabling Pester to mock it:

```PowerShell
BeforeAll {
	Function Get-AzureADUser {
	}

	Function Remove-AzureAdUser {
	}
}
```

The end result, in this instance, is as follows:

```PowerShell
Running tests from '/Users/ryan/Development/PowerShell/Azure.Test.ps1'
Describing Azure script
 Context When parameters are correct
   [+] Should remove the Azure AD User 12ms (6ms|6ms)
Tests completed in 35ms
Tests Passed: 1, Failed: 0, Skipped: 0 NotRun: 0
```
## Conclusion
Pester is a robust framework empowering comprehensive testing of PowerShell code, thereby enhancing overall quality. Did you know that you can also incorporate Pester within your Azure DevOps pipeline results? Additional details can be found here: [Logit Blog - Increase the success rate of Azure DevOps pipelines using Pester](https://www.logitblog.com/increase-the-success-rate-of-azure-devops-pipelines-using-pester/){:target="_blank"}

Should you encounter a `CommandNotFoundException` error during a Pester test, it simply indicates the unavailability of the commandlet. You can address this by either creating a basic dummy function within the test or ensuring the module's availability.

Credit to [Niels Reloe](https://www.linkedin.com/in/niels-reloe-12b67a66/){:target="_blank"}. Together, we resolved this issue and continued our journey toward achieving 100% code coverage!

I trust this explanation has been helpful. Feel free to leave any questions in the comments section below.

Photo by [Anil Sharma](https://unsplash.com/@anil_sharma_india?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"} on [Unsplash](https://unsplash.com/photos/fE3uYk0Ri1U?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"}
