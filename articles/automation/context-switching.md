---
title: Changement de contexte dans Azure Automation
description: Cet article décrit le changement de contexte et comment éviter les problèmes liés aux runbooks.
services: automation
ms.subservice: process-automation
ms.date: 09/27/2021
ms.topic: conceptual
ms.openlocfilehash: 121e302708fecd20934f6ba57bd08590e45a7429
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233197"
---
# <a name="context-switching-in-azure-automation"></a>Changement de contexte dans Azure Automation

Un changement de contexte survient lorsque le contexte dans un processus modifie le contexte dans un autre processus. Un contexte Azure est un ensemble d’informations qui définit la cible des cmdlets Azure Powershell. Le contexte est constitué des propriétés suivantes :

|Propriété | Description |
|---|---|
|Nom | Nom du contexte.|
|Compte | Nom d’utilisateur ou principal du service utilisé pour authentifier les communications avec Azure.|
|Environnement | Représente le cloud global Azure ou l’un des clouds Azure nationaux, par exemple, Azure Government. Vous pouvez également spécifier une plateforme cloud hybride, telle qu’Azure Stack.|
|Abonnement | Représente l’abonnement Azure qui contient les ressources que vous souhaitez gérer.|
|Locataire | Une instance dédiée et approuvée d’Azure Active Directory qui représente une organisation unique.|
|Informations d'identification | Les informations dont Azure se sert pour vérifier votre identité et pour confirmer votre autorisation d’accès à des ressources dans Azure.|

Lorsqu’un compte qui peut accéder à plusieurs abonnements se connecte, ces abonnements peuvent être ajoutés au contexte de l’utilisateur. Pour garantir l’abonnement approprié, vous devez le déclarer lors de la connexion. Par exemple, utilisez `Add-AzAccount -Credential $Cred -subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749'`. Toutefois, des problèmes peuvent survenir lorsque vos runbooks gérant un abonnement sont exécutés dans le même processus de bac à sable que vos autres runbooks qui gèrent les ressources d’un autre abonnement à partir du même compte Automation. Les modifications apportées au contexte effectuées par un runbook peuvent affecter vos autres runbooks en utilisant le contexte par défaut. Comme le contexte contient des informations, telles que les informations d’identification à utiliser et l’abonnement à la cible, les applets de commande peuvent cibler un abonnement incorrect, ce qui peut entraîner des erreurs de type `not found` ou d’autorisation. Ce problème est appelé **changement de contexte**.

## <a name="manage-azure-contexts"></a>Gérer les contextes Azure

Pour éviter que vos runbooks ne soient exécutés sur les ressources de l’abonnement incorrect, consultez les recommandations suivantes :

1. Désactivez l’enregistrement du contexte du bac à sable dans votre runbook Automation à l’aide de la commande suivante au début de chaque runbook : `Disable-AzContextAutosave -Scope Process`.
1. Les applets de commande Azure PowerShell prennent en charge le paramètre `-DefaultProfile`. Ce paramètre a été ajouté à toutes les applets de commande Az et Azure Resource Manager pour prendre en charge l’exécution de plusieurs scripts dans le même processus. Cela vous permet de spécifier le contexte à utiliser pour chaque applet de commande. Enregistrez votre objet de contexte dans votre runbook lorsqu’il est créé et chaque fois qu’il est modifié. Référencez-le ensuite dans chaque appel effectué à l’aide de l’applet de commande AZ ou AzureRM. Par exemple : `$AzureContext = Set-AzContext -SubscriptionId $subID`.
1. Transmettez l’objet de contexte à l’applet de commande PowerShell, par exemple `Get-AzVM -ResourceGroupName "myGroup" -DefaultProfile $AzureContext`.

Voici un extrait de code de runbook PowerShell qui utilise une identité managée affectée par le système qui suit les recommandations pour éviter le changement de contexte.

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

# Pass context object - even though the context had just been set
# This is the step that guarantees the context will not be switched.
Get-AzVM -ResourceGroupName "resourceGroupName" -DefaultProfile $AzureContext | Select Name
```

## <a name="possible-symptoms"></a>Symptômes possibles

Bien que vous puissiez ne pas rencontrer de problème si vous ne suivez pas ces recommandations, ce risque existe bel et bien. Le problème sous-jacent est lié au timing. Tout dépend de ce que fait chaque runbook au moment où l’autre runbook change de contexte. Voici quelques message d’erreur possibles : Toutefois, ces messages d’erreur peuvent aussi apparaître en lien avec des conditions non liées au changement de contexte.

`The subscription named <subscription name> cannot be found.`

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

```error
Get-AzureRmResource : Resource group "SomeResourceGroupName" could not be found.
... resources = Get-AzResource -ResourceGroupName $group.ResourceGro ...
                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Get-AzResource], CloudException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.ResourceManager.Cmdlets.Implementation.GetAzureResourceCmdlet
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’authentification de compte Azure Automation](automation-security-overview.md)
- [Exécution d’un runbook dans Azure Automation](automation-runbook-execution.md)
- [Gérer les modules dans Azure Automation](./shared-resources/modules.md)

