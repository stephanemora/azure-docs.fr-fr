---
title: Ressources d’informations d’identification dans Azure Automation
description: Les ressources d’informations d’identification dans Azure Automation contiennent des informations d’identification de sécurité qui peuvent être utilisées pour s’authentifier auprès des ressources auxquelles le Runbook ou la configuration DSC a accès. Cet article décrit comment créer des ressources d’informations d’identification et les utiliser dans un Runbook ou une configuration DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c8b63a2676690004d23094b490fea0ef150ab9cb
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546399"
---
# <a name="credential-assets-in-azure-automation"></a>Ressources d’informations d’identification dans Azure Automation

Une ressource d’informations d’identification Automation détient un objet constitué d’informations d’identification de sécurité, comme un nom d’utilisateur et un mot de passe. Les runbooks et les configurations DSC utilisent des applets de commande qui acceptent un objet [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) pour l’authentification. Ils peuvent aussi extraire le nom d’utilisateur et le mot de passe de l’objet `PSCredential` à fournir à une application ou à un service nécessitant une authentification. 

Azure Automation stocke de manière sécurisée les propriétés des informations d’identification. L’accès aux propriétés via un runbook ou une configuration DSC dépend de l’activité [Get-AutomationPSCredential](#activities-used-to-access-credentials).

> [!NOTE]
> Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation en utilisant une clé unique générée pour chaque compte Automation. Cette clé est stockée dans Key Vault. Avant de stocker une ressource sécurisée, la clé est chargée à partir de Key Vault, puis utilisée pour chiffrer la ressource.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Applets de commande Azure PowerShell Az utilisées pour les ressources d’informations d’identification

Les applets de commande intégrées au module Azure PowerShell Az et présentées dans le tableau suivant permettent de créer et gérer les ressources d’informations d’identification Automation avec Windows PowerShell. Elles sont incluses dans le [module Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), qui peut être utilisé dans les runbooks et les configurations DSC Automation. Consultez [Prise en charge de modules Az dans Azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Applet de commande | Description |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Récupère des informations sur une ressource d’informations d’identification. Cette applet de commande ne retourne pas un objet `PSCredential`.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Crée de nouvelles informations d’identification Automation. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Supprime des informations d’identification Automation. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Définit les propriétés d’informations d’identification Automation existantes. |

## <a name="activities-used-to-access-credentials"></a>Activités utilisées pour accéder aux informations d’identification

Les activités présentées dans le tableau suivant permettent d’accéder aux informations d’identification dans les runbooks et les configurations DSC.

| Activité | Description |
|:--- |:--- |
| `Get-AutomationPSCredential` |Obtient les informations d’identification à utiliser dans un Runbook ou dans une configuration DSC. Les informations d’identification se présentent sous la forme d’un objet `PSCredential`. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Obtient des informations d’identification avec une invite pour le nom d’utilisateur et le mot de passe. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Crée des informations d’identification. |

Pour un développement local à l’aide d’Azure Automation Authoring Toolkit, l’applet de commande `Get-AutomationPSCredential` fait partie de l’assembly [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). Pour Azure utilisant le contexte d’Automation, l’applet de commande se trouve dans `Orchestrator.AssetManagement.Cmdlets`. Consultez [Gérer les modules dans Azure Automation](modules.md).

Pour récupérer les objets `PSCredential` de votre code, vous pouvez installer le module complémentaire [Microsoft Azure Automation ISE pour PowerShell ISE](https://github.com/azureautomation/azure-automation-ise-addon).

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

Votre script peut aussi importer le module nécessaire, le cas échéant, comme dans l’exemple suivant : 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Il est déconseillé d’utiliser des variables dans le paramètre `Name` de `Get-AutomationPSCredential`. Leur utilisation peut contrarier la découverte des dépendances entre les runbooks ou les configurations DSC et les informations d’identification au moment de la conception.

## <a name="python2-functions-that-access-credentials"></a>Fonctions Python2 qui accèdent aux informations d’identification

La fonction dans le tableau suivant est utilisée pour accéder aux informations d’identification dans un runbook Python2.

| Fonction | Description |
|:---|:---|
| `automationassets.get_automation_credential` | Récupère des informations sur une ressource d’informations d’identification. |

> [!NOTE]
> Importez le module `automationassets` en haut de votre runbook Python pour accéder aux fonctions des ressources.

## <a name="creating-a-new-credential-asset"></a>Création d’une ressource d’informations d’identification

Vous pouvez créer une ressource d’informations d’identification à partir du portail Azure ou de Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Créer une ressource d’informations d’identification avec le portail Azure

1. À partir de votre compte Automation, sélectionnez **Informations d’identification** sous **Ressources partagées**.
1. Sélectionnez **Ajouter des informations d’identification**.
2. Dans le volet Nouvelles informations d’identification, entrez un nom d’informations d’identification approprié en suivant votre convention de nommage. 
3. Tapez votre ID d’accès dans le champ **Nom d’utilisateur**. 
4. Pour les deux champs de mot de passe, entrez votre clé d’accès secrète.

    ![Créer de nouvelles informations d’identification](../media/credentials/credential-create.png)

5. Si la case Authentification multifacteur est cochée, décochez-la. 
6. Cliquez sur **Créer** pour enregistrer la nouvelle ressource d’informations d’identification.

> [!NOTE]
> Azure Automation ne prend pas en charge les comptes d’utilisateurs qui utilisent l’authentification multifacteur.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Créer une ressource d’informations d’identification avec Windows PowerShell

Les exemples suivants montrent comment créer une ressource d’informations d’identification Automation. Un objet `PSCredential` est d’abord créé à partir du nom et du mot de passe, et est ensuite utilisé pour créer la ressource d’informations d’identification. Au lieu de cela, vous pouvez utiliser l’applet de commande `Get-Credential` pour inviter l’utilisateur à taper un nom et un mot de passe.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Utilisation des informations d’identification PowerShell

Un runbook ou une configuration DSC récupère une ressource d’informations d’identification avec l’activité `Get-AutomationPSCredential`. Cette activité récupère un objet `PSCredential` que vous pouvez utiliser avec une activité ou une applet de commande qui nécessite des informations d’identification. Vous pouvez également récupérer les propriétés de l’objet d’informations d’identification pour les utiliser individuellement. L’objet a des propriétés pour le nom d’utilisateur et le mot de passe sécurisé. Vous pouvez aussi utiliser la méthode [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) pour récupérer un objet [NetworkCredential](/dotnet/api/system.net.networkcredential) qui représente une version non sécurisée du mot de passe.

> [!NOTE]
> `Get-AzAutomationCredential` ne récupère pas un objet `PSCredential` pouvant être utilisé pour l’authentification. Elle fournit uniquement des informations sur les informations d’identification. Si vous devez utiliser des informations d’identification dans un runbook, vous devez les récupérer sous la forme d’un objet `PSCredential` à l’aide de `Get-AutomationPSCredential`.

### <a name="textual-runbook-example"></a>Exemple de runbook textuel

L’exemple suivant montre comment utiliser des informations d’identification PowerShell dans un runbook. Il récupère les informations d’identification et affecte le nom d’utilisateur et mot de passe associés à des variables.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Vous pouvez également utiliser des informations d’identification pour vous authentifier auprès d’Azure avec la cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Dans la plupart des cas, vous devez utiliser un [compte d’identification](../manage-runas-account.md) et récupérer la connexion avec [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Exemple de runbook graphique

Vous ajoutez une activité `Get-AutomationPSCredential` à un runbook graphique en cliquant avec le bouton droit sur les informations d’identification dans le volet Bibliothèque de l’éditeur graphique et en sélectionnant **Ajouter au canevas**.

![Ajout d’informations d’identification à la zone de dessin](../media/credentials/credential-add-canvas.png)

L’image suivante montre un exemple d’utilisation d’informations d’identification dans un Runbook graphique. Dans ce cas, les informations d’identification permettent à un runbook de s’authentifier auprès de ressources Azure, comme décrit dans [Utiliser Azure AD dans Azure Automation pour s’authentifier dans Azure](../automation-use-azure-ad.md). La première activité récupère les informations d’identification ayant accès à l’abonnement Azure. L’activité de connexion de compte utilise ensuite ces informations d’identification pour assurer l’authentification pour les activités qui suivent. Un [lien de pipeline](../automation-graphical-authoring-intro.md#links-and-workflow) est utilisé ici, car le paramètre `Get-AutomationPSCredential` attend un seul objet.  

![Ajout d’informations d’identification à la zone de dessin](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Utilisation d’informations d’identification dans une configuration DSC

Même si les configurations DSC dans Azure Automation peuvent utiliser des ressources d’informations d’identification au moyen de `Get-AutomationPSCredential`, elles peuvent aussi transmettre des ressources d’informations d’identification via des paramètres. Pour plus d’informations, consultez [Compilation de configurations dans Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Utilisation des informations d’identification dans Python2

L’exemple suivant montre comment accéder à des informations d’identification dans des runbooks Python2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les liens dans la création de graphiques, consultez [Liens dans la création de graphiques](../automation-graphical-authoring-intro.md#links-and-workflow).
* Pour comprendre les différentes méthodes d’authentification pour Automation, consultez [Sécurité dans Azure Automation](../automation-security-overview.md).
* Pour une prise en main des runbooks graphiques, voir [Mon premier runbook graphique](../automation-first-runbook-graphical.md).
* Pour obtenir des informations sur la prise en main des Runbooks de workflow PowerShell, voir [Mon premier runbook PowerShell Workflow](../automation-first-runbook-textual.md).
* Pour commencer avec les runbooks Python2, consultez [My first Python2 runbook](../automation-first-runbook-textual-python2.md) (Mon premier Runbook Python2). 
