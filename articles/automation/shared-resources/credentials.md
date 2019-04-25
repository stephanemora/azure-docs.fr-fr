---
title: Ressources d’informations d’identification dans Azure Automation
description: Les ressources d’informations d’identification dans Azure Automation contiennent des informations d’identification de sécurité qui peuvent être utilisées pour s’authentifier auprès des ressources auxquelles le Runbook ou la configuration DSC a accès. Cet article décrit comment créer des ressources d’informations d’identification et les utiliser dans un Runbook ou une configuration DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6465fd069e73a571e0671d528f5d0b4da4602dc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60499402"
---
# <a name="credential-assets-in-azure-automation"></a>Ressources d’informations d’identification dans Azure Automation

Une ressource d’informations d’identification Automation conserve un objet qui contient les informations d’identification de sécurité telles qu’un nom d’utilisateur et le mot de passe. Les Runbooks et les configurations DSC peuvent utiliser les applets de commande qui acceptent un objet PSCredential pour l’authentification, ou ils peuvent extraire le nom d’utilisateur et le mot de passe de l’objet PSCredential pour les fournir à l’application ou au service nécessitant l’authentification. Les propriétés d’informations d’identification sont stockées de manière sécurisée dans Azure Automation et sont accessibles dans le Runbook ou la configuration DSC avec l’activité [Get-AutomationPSCredential](#activities) .

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l’aide d’une clé unique générée pour chaque compte Automation. Cette clé est stockée dans Key Vault. Avant de stocker une ressource sécurisée, la clé est chargée à partir de Key Vault, puis utilisée pour chiffrer la ressource.

## <a name="azure-classic-powershell-cmdlets"></a>Applets de commande PowerShell Azure Classic

Les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources d’informations d’identification Automation avec Windows PowerShell.  Elles font partie de la [module Azure PowerShell](/powershell/azure/overview), qui est disponible pour une utilisation dans des runbooks Automation et les configurations DSC.

| Applets de commande | Description |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |Récupère des informations sur une ressource d’informations d’identification. Vous pouvez uniquement récupérer les informations d’identification elles-mêmes à partir de l’activité **Get-AutomationPSCredential** . |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Crée de nouvelles informations d’identification Automation. |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Supprime des informations d’identification Automation. |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Définit les propriétés d’informations d’identification Automation existantes. |

## <a name="azurerm-powershell-cmdlets"></a>Applets de commande AzureRM PowerShell

Pour AzureRM, les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources d’informations d’identification Automation avec Windows PowerShell.  Elles font partie de la [module AzureRM.Automation](/powershell/azure/overview), qui est disponible pour une utilisation dans des runbooks Automation et les configurations DSC.

| Applets de commande | Description |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |Récupère des informations sur une ressource d’informations d’identification. Cela ne retourne pas un objet PSCredential.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |Crée de nouvelles informations d’identification Automation. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |Supprime des informations d’identification Automation. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |Définit les propriétés d’informations d’identification Automation existantes. |

## <a name="activities"></a>Activités

Les activités dans le tableau suivant sont utilisées pour accéder aux informations d’identification dans un Runbook ou dans des configurations DSC.

| Activités | Description |
|:--- |:--- |
| Get-AutomationPSCredential |Obtient les informations d’identification à utiliser dans un Runbook ou dans une configuration DSC. Renvoie un objet [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) . |

> [!NOTE]
> Évitez d’utiliser des variables dans le paramètre –Name de Get-AutomationPSCredential, car cela complique la découverte des dépendances entre les Runbooks ou configurations DSC et les ressources d’informations d’identification au moment de la conception.

## <a name="python2-functions"></a>Fonctions Python2

La fonction dans le tableau suivant est utilisée pour accéder aux informations d’identification dans un runbook Python2.

| Fonction | Description |
|:---|:---|
| automationassets.get_automation_credential | Récupère des informations sur une ressource d’informations d’identification. |

> [!NOTE]
> Vous devez importer le module « automationassets » en haut de votre runbook Python afin d’accéder aux fonctions des ressources.

## <a name="creating-a-new-credential-asset"></a>Création d’une ressource d’informations d’identification

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Pour créer une ressource d’informations d’identification avec le portail Azure

1. Dans votre compte Automation, sélectionnez **Informations d’identification** sous **Ressources partagées**.
1. Cliquez sur **+ Ajouter des informations d’identification**.
1. Remplissez le formulaire, puis cliquez sur **Créer** pour enregistrer les nouvelles informations d’identification.

> [!NOTE]
> Les comptes d’utilisateurs qui ont recours à l’authentification multifacteur ne sont pas pris en charge pour une utilisation dans Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Pour créer une ressource d’informations d’identification PowerShell avec Windows PowerShell

Les exemples de commandes suivants montrent comment créer des informations d’identification Automation. Un objet PSCredential est d’abord créé avec le nom et le mot de passe, puis il est utilisé pour créer la ressource d’informations d’identification. Vous pouvez également utiliser l’applet de commande **Get-Credential** pour être invité à saisir un nom et un mot de passe.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Utilisation des informations d’identification PowerShell

Vous récupérez une ressource d’informations d’identification dans un Runbook ou une configuration DSC avec l’activité **Get-AutomationPSCredential**. Cette propriété renvoie un [objet PSCredential](/dotnet/api/system.management.automation.pscredential) que vous pouvez utiliser avec une activité ou une applet de commande nécessitant un paramètre PSCredential. Vous pouvez également récupérer les propriétés de l’objet d’informations d’identification pour les utiliser individuellement. L’objet possède une propriété pour le nom d’utilisateur et le mot de passe sécurisé. Vous pouvez également utiliser la méthode **GetNetworkCredential** pour retourner un objet [NetworkCredential](/dotnet/api/system.net.networkcredential) fournissant une version non sécurisée du mot de passe.

> [!NOTE]
> **Get-AzureRmAutomationCredential** ne retourne pas une **PSCredential** qui peut être utilisé pour l’authentification. Il fournit uniquement des informations sur les informations d’identification. Si vous avez besoin d’utiliser les informations d’identification dans un runbook, vous devez utiliser le **Get-AutomationPSCredential** pour récupérer le **PSCredential** objet.

### <a name="textual-runbook-sample"></a>Exemple de Runbook textuel

Les exemples de commandes suivants montrent comment utiliser les informations d’identification PowerShell dans un Runbook. Dans cet exemple, les informations d’identification sont récupérées et le nom d’utilisateur et le mot de passe affectés à des variables.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Vous pouvez également utiliser les informations d’identification pour s’authentifier sur Azure avec [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Dans la plupart des cas, vous devez utiliser un [exécuter en tant que compte](../manage-runas-account.md) et les récupérer avec [Get-AutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential`
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Exemple de Runbook graphique

Vous ajoutez une activité **Get-AutomationPSCredential** à un Runbook graphique en cliquant avec le bouton droit sur les informations d’identification dans le volet Bibliothèque de l’éditeur graphique et en sélectionnant **Ajouter à la zone de dessin**.

![Ajout d’informations d’identification à la zone de dessin](../media/credentials/credential-add-canvas.png)

L’image suivante montre un exemple d’utilisation d’informations d’identification dans un Runbook graphique.  Dans ce cas, il est utilisé pour l’authentification d’un runbook à des ressources Azure comme décrit dans [authentifier des Runbooks avec un compte d’utilisateur Azure AD](../automation-create-aduser-account.md).  La première activité récupère les informations d’identification ayant accès à l’abonnement Azure.  L’activité **Add-AzureAccount** utilise ensuite ces informations d’identification pour fournir l’authentification pour toutes les activités qui la suivent.  C’est un [lien pipeline](../automation-graphical-authoring-intro.md#links-and-workflow) , étant donné que **Get-AutomationPSCredential** attend un objet unique.  

![Ajout d’informations d’identification à la zone de dessin](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Utilisation des informations d’identification PowerShell dans une configuration DSC

Tandis que les configurations DSC dans Azure Automation peuvent faire référence à des ressources d’informations d’identification à l’aide de **Get-AutomationPSCredential**, les ressources d’informations d’identification peuvent également être transmis dans via des paramètres, si vous souhaitiez. Pour plus d’informations, consultez [Compilation de configurations dans Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Utilisation des informations d’identification dans Python2

L’exemple suivant montre un exemple d’accès aux informations d’identification dans les runbooks Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les liens lors de la création graphique, consultez [Liens lors de la création graphique](../automation-graphical-authoring-intro.md#links-and-workflow)
* Pour comprendre les différentes méthodes d’authentification avec Automation, consultez [Sécurité dans Azure Automation](../automation-security-overview.md)
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](../automation-first-runbook-graphical.md)
* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](../automation-first-runbook-textual.md)
* Pour commencer avec les runbooks Python2, consultez [My first Python2 runbook](../automation-first-runbook-textual-python2.md) (Mon premier Runbook Python2). 
