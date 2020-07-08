---
layout: post
hidden: false
title: Collect performance data from Nutanix AHV
authors: [ryan]
comments: true
categories: [Nutanix]
tags: [Nutanix, Nutanix, Powershell, PRISM]
image: assets/images/posts/2019-03-29-collect-performance-data-from-nutanix-ahv/collect-performance-data-from-nutanix-ahv-feature-image.png
---
Performance data is a recurring topic on my blog. It is important to have data from multiple sources when doing performance analysis. Collecting the performance data from a Nutanix environment running AHV is a bit different compared to VMware ESX or Citrix XenServer. This blog post will cover how to collect the performance data from a Nutanix AHV hypervisor using PowerShell.

## Collecting the data from Nutanix AHV
As mentioned in the introduction collecting performance data is a bit different on Nutanix AHV. Nutanix uses an API that allows you to collect the data from a cluster or specific node. Nutanix provides API reference documentation on the developer portal which shows all the available options.

<a href="https://developer.nutanix.com/reference/prism_element/v2/" target="_blank" rel="noopener noreferrer">Nutanix REST API Explorer</a>

![api-reference]({{site.baseurl}}/assets/images/posts/2019-03-29-collect-performance-data-from-nutanix-ahv/nutanix-developert-api-reference.png)

The goal of the script is to collect the performance data from a specific node and store the results in CSV file. The first step is to collect the node unique identifier, known as the uuid. This can be done using the /hosts API call which collects all the available nodes in the cluster. More information about can be found here: <a href="https://developer.nutanix.com/reference/prism_element/v2/api/hosts/get-hosts-gethosts" target="_blank" rel="noopener noreferrer">https://developer.nutanix.com/reference/prism_element/v2/api/hosts/get-hosts-gethosts</a>

As you may have noticed in the documentation it also provides the current stats from the node. It is possible to use this API call but it is recommended to use the specific stats call as this contains all the nodes including other information that is not required. The results will contain the uuid of the node but in the example below, it is also used to collect all available metrics for the next call. The final step is to collect all the metrics using the /hosts/uuid/stats call. More information about this call can be found here: <a href="https://developer.nutanix.com/reference/prism_element/v2/api/hosts/get-hosts-uuid-stats-gethoststats" target="_blank" rel="noopener noreferrer">https://developer.nutanix.com/reference/prism_element/v2/api/hosts/get-hosts-uuid-stats-gethoststats</a>

The following PowerShell script will collect all the available metrics from a specific node.
<pre class="lang:ps decode:true">$cred = Get-Credential
$prismElements = "cluster1.logitblog.com"
$node = "node-01"
$file = "C:\Temp\Example.csv" 
$samples = 10
$interval = 30

[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12     

$username = $cred.UserName
$password = $cred.GetNetworkCredential().Password
$header = @{
    Authorization = "Basic " + [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($username + ":" + $password))
}   

$uriHost = "https://$($prismElements):9440/PrismGateway/services/rest/v2.0/hosts"

$nodes = Invoke-RestMethod -Method Get -Uri $uriHost -Headers $header
$node = $nodes.entities | Where-Object {$_.name -eq $node}

$stats = $node.stats | Get-Member | Where-Object {$_.MemberType -eq "NoteProperty"} | Select Name
$baseUri = "https://$($prismElements):9440/PrismGateway/services/rest/v2.0/hosts/$($node.uuid)/stats/?"
foreach ($stat in $stats.Name) {
    $baseUri = $baseUri + "metrics=" + $stat + "&amp;"
}
$uri =  $baseUri.Substring(0, $baseUri.Length -1)

$i = 0
do {
    $results = Invoke-RestMethod -Method GET -Uri $baseUri -Header $header

    $item = New-Object PSObject    
    $item | Add-Member Noteproperty DateTime $dateTime.ToString('MM/dd/yyyy HH:mm:ss')
    foreach ($result in $results.stats_specific_responses) {
        $item | Add-Member Noteproperty $result.metric $result.values[0]
    }

    $item | Export-Csv -Path $file -NoTypeInformation -Append
    
    Start-Sleep $interval
    $i++
} while ($i -le $samples)
</pre>
The script will report all available metrics with to a CSV format which looks like this.

![ahv-metrics]({{site.baseurl}}/assets/images/posts/2019-03-29-collect-performance-data-from-nutanix-ahv/nutanix-ahv-metrics-csv.png)
Please note, the CPU Usage is not reported in a percentage and should be divided by 10000.

## Conclusion

Performance data is one of the basics when doing performance research. Nutanix AHV is a bit different compared to other hypervisors like VMware ESX or Citrix XenServer. With the API the data can be collected and used in any format you prefer. In order to collect all the metrics from the stats call you have to specify the metrics that need to be collected.

I love the modern way of collecting the data using an API but the available metrics on the node is a bit disappointing. The basic crucial metrics are there, but comparing to VMware ESXtop it is basically nothing. During the creation of the script, I also noticed it can take some time when invoking the REST call. With the example interval, it is not a problem but it would when the interval is 5 seconds. Personally, I would love to see a similar tool as VMware ESXtop for Nutanix AHV that contains a ton of metrics just running on the node.

If you any questions or comments, please leave them below.

Photo by <a href="https://unsplash.com/photos/mrIaqKh9050?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener noreferrer">Johannes Groll</a> on <a href="https://unsplash.com/?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText" target="_blank" rel="noopener noreferrer">Unsplash</a>
