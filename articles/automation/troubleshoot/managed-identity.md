---
title: Résoudre les problèmes d’identité managée Azure Automation (préversion)
description: Cet article explique comment dépanner et résoudre les problèmes liés à l’utilisation d’une identité managée avec un compte Automation.
services: automation
ms.subservice: ''
ms.date: 04/28/2021
ms.topic: troubleshooting
ms.openlocfilehash: 719b20190194779b834eb3de996c0920d50ad313
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110787351"
---
# <a name="troubleshoot-azure-automation-managed-identity-issues-preview"></a>Résoudre les problèmes d’identité managée Azure Automation (préversion)

Cet article traite des solutions aux problèmes que vous pourriez rencontrer en utilisant une identité managée avec votre compte Automation. Pour obtenir des informations générales sur l’utilisation d’une identité managée avec des comptes Automation, consultez [Vue d’ensemble de l’authentification de compte Azure Automation](../automation-security-overview.md#managed-identities-preview).

## <a name="scenario-attempt-to-use-managed-identity-with-automation-account-fails"></a>Scénario : Échec de la tentative d’utilisation d’une identité managée avec un compte Automation

### <a name="issue"></a>Problème

Lorsque vous essayez d’utiliser des identités managées dans votre compte Automation, vous rencontrez une erreur semblable à celle-ci :

```error
Connect-AzureRMAccount : An error occurred while sending the request. At line:2 char:1 + Connect-AzureRMAccount -Identity + 
CategoryInfo : CloseError: (:) [Connect-AzureRmAccount], HttpRequestException + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>Cause

La cause la plus courante est que vous n’avez pas activé l’identité avant d’essayer de l’utiliser. Pour vérifier cela, exécutez le runbook PowerShell suivant dans le compte Automation concerné.

```powershell
resource= "?resource=https://management.azure.com/"
$url = $env:IDENTITY_ENDPOINT + $resource
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER)
$Headers.Add("Metadata", "True")

try
{
    $Response = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
}
catch
{
    $StatusCode = $_.Exception.Response.StatusCode.value__
    $stream = $_.Exception.Response.GetResponseStream()
    $reader = New-Object System.IO.StreamReader($stream)
    $responseBody = $reader.ReadToEnd()
    
    Write-Output "Request Failed with Status: $StatusCode, Message: $responseBody"
}
```

Si le problème est que vous n’avez pas activé l’identité avant de tenter de l’utiliser, vous devriez obtenir un résultat similaire à ce qui suit :

`Request Failed with Status: 400, Message: {"Message":"No managed identity was found for Automation account xxxxxxxxxxxx"}`

### <a name="resolution"></a>Résolution

Vous devez activer une identité pour votre compte Automation avant de pouvoir utiliser le service d’identité managée. Voir [Activer une identité managée pour votre compte Azure Automation (préversion)](../enable-managed-identity-for-automation.md).

## <a name="next-steps"></a>Étapes suivantes

Si cet article ne vous permet pas de résoudre votre problème, utilisez l’un des canaux suivants pour obtenir une aide supplémentaire :

* Obtenez des réponses de la part d’experts Azure via les [Forums Azure](https://azure.microsoft.com/support/forums/).
* Contactez [@AzureSupport](https://twitter.com/azuresupport). Il s’agit du compte Microsoft Azure officiel qui permet à la communauté Azure d’accéder aux ressources dont elle a besoin : réponses, support et experts.
* Signaler un incident au support Azure Accédez au [site du support Azure](https://azure.microsoft.com/support/options/), puis sélectionnez **Obtenir de l’aide**.