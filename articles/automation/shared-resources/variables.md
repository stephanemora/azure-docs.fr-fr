---
title: Ressources de variables dans Azure Automation
description: Les ressources de variables sont des valeurs disponibles pour tous les Runbooks et configurations DSC d’Azure Automation.  Cet article présente les variables de façon détaillée et comment les utiliser dans une création textuelle ou graphique.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4a4a92feb3e1b400c0f40076148f7898c4bdef1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365828"
---
# <a name="variable-assets-in-azure-automation"></a>Ressources de variables dans Azure Automation

Les ressources de variables sont des valeurs disponibles pour tous les runbooks et configurations DSC de votre compte Automation. Vous pouvez les gérer à partir du portail Azure, à partir de PowerShell, au sein d’un runbook ou dans une configuration DSC.

Les variables Automation sont utiles pour les scénarios suivants :

- Partage d’une valeur entre plusieurs runbooks ou configurations DSC.

- Partage d’une valeur entre plusieurs tâches du même runbook ou de la même configuration DSC.

- Gestion d’une valeur utilisée par des runbooks ou des configurations DSC à partir du portail ou de la ligne de commande PowerShell. Un ensemble d’éléments de configuration communs, comme la liste spécifique des noms de machines virtuelles, un groupe de ressources spécifique, un nom de domaine Active Directory, etc, constitue un exemple.  

Azure Automation conserve les variables et les rend disponibles même en cas d’échec d’une configuration runbook ou DSC. Ce comportement permet à un runbook ou à une configuration DSC de définir une valeur par la suite utilisée par un autre runbook, ou par le même runbook ou la même configuration DSC lors de son exécution suivante.

Azure Automation stocke chaque variable chiffrée de manière sécurisée. Lorsque vous créez une variable, vous pouvez spécifier son mode de chiffrement et de stockage par Azure Automation sous la forme d’une ressource sécurisée. Les ressources sécurisées incluent des informations d’identification, des certificats et des connexions. Azure Automation chiffre ces ressources et les stocke à l’aide d’une clé unique générée pour chaque compte Automation. La clé est stockée dans un coffre de clés géré par le système. Avant de stocker une ressource sécurisée, Azure Automation charge la clé à partir du coffre de clés, puis l’utilise pour chiffrer la ressource. 

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Types de variables

Lorsque vous créez une variable avec le portail Azure, vous devez spécifier un type de données dans la liste déroulante afin que le portail puisse afficher le contrôle approprié pour l’entrée de la valeur de la variable. Voici les types de variables disponibles dans Azure Automation :

* String
* Integer
* DateTime
* Boolean
* Null

La variable n’est pas limitée au type de données désigné. Vous devez définir la variable à l'aide de Windows PowerShell si vous souhaitez spécifier une valeur d'un type différent. Si vous indiquez `Not defined`, la valeur de la variable est définie sur Null, et vous devez définir la valeur avec l’applet de commande [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) ou l’activité `Set-AutomationVariable`.

Vous ne pouvez pas utiliser le portail Azure pour créer ou modifier la valeur d’un type de variable complexe. Toutefois, vous pouvez fournir une valeur de n’importe quel type à l’aide de Windows PowerShell. Les types complexes sont récupérés en tant que [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Vous pouvez stocker plusieurs valeurs dans une seule variable en créant un tableau ou une table de hachage, et en l'enregistrant sur la variable.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Applets de commande PowerShell qui créent et gèrent des ressources de variables

Pour le module Az, les applets de commande figurant dans le tableau suivant sont utilisées pour créer et gérer les ressources de variables Automation avec Windows PowerShell. Elles font partie du [module Az.Automation](/powershell/azure/overview), utilisable dans les runbooks Automation et les configurations DSC.

| Applet de commande | Description |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Récupère la valeur d'une variable existante. Vous ne pouvez pas utiliser cette applet de commande pour récupérer la valeur d’une variable chiffrée. La seule façon de procéder consiste à utiliser l’activité `Get-AutomationVariable` dans un runbook ou une configuration DSC. |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Crée une variable et définit sa valeur.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Supprime une variable existante.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Définit la valeur d'une variable existante. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Activités pour accéder aux variables des runbooks et des configurations DSC

Les activités figurant dans le tableau suivant sont utilisées pour accéder à des variables dans des runbooks et des configurations DSC. Les applets de commande de ces activités sont fournies avec le module global `Orchestrator.AssetManagement.Cmdlets`.

| Activité | Description |
|:---|:---|
|`Get-AutomationVariable`|Récupère la valeur d'une variable existante.|
|`Set-AutomationVariable`|Définit la valeur d'une variable existante.|

> [!NOTE]
> Évitez d’utiliser des variables dans le paramètre `Name` de `Get-AutomationVariable` dans un runbook ou une configuration DSC. L’utilisation de ce paramètre peut compliquer la découverte des dépendances entre les runbooks ou les configurations DSC et les variables Automation au moment de la conception.

Notez que `Get-AutomationVariable` ne fonctionne pas dans PowerShell, mais uniquement dans un runbook ou une configuration DSC. Par exemple, pour voir la valeur d’une variable chiffrée, vous pouvez créer un runbook pour obtenir cette variable, puis l’écrire dans le flux de sortie :
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Fonctions d’accès aux variables dans les runbooks Python 2

Les fonctions du tableau suivant sont utilisées pour accéder aux variables d’un runbook Python 2.

|Fonctions Python 2|Description|
|:---|:---|
|`automationassets.get_automation_variable`|Récupère la valeur d'une variable existante. |
|`automationassets.set_automation_variable`|Définit la valeur d'une variable existante. |

> [!NOTE]
> Vous devez importer le module `automationassets` en haut de votre runbook Python pour accéder aux fonctions des ressources.

## <a name="working-with-automation-variables"></a>Utilisation des variables Automation

>[!NOTE]
>Si vous voulez supprimer le chiffrement d’une variable, vous devez supprimer la variable et la recréer non chiffrée.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Créer une variable à l’aide du portail Azure

1. À partir de votre compte Automation, cliquez sur la vignette **Ressources** puis, dans le panneau **Ressources**, sélectionnez **Variables**.
2. Sur la vignette **Variables**, sélectionnez **Ajouter une variable**.
3. Définissez les options dans le panneau **Nouvelle variable** et cliquez sur **Créer** pour enregistrer la nouvelle variable.

> [!NOTE]
> Une fois que vous avez enregistré une variable chiffrée, vous ne pouvez plus la voir dans le portail. Elle peut uniquement être mise à jour.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Créer et utiliser une variable dans Windows PowerShell

Un script PowerShell utilise l’applet de commande `New-AzAutomationVariable`, ou son équivalent dans le module AzureRM, pour créer une variable et définir sa valeur initiale. Si la variable est chiffrée, l’appel doit utiliser le paramètre `Encrypted`.

Le script récupère la valeur de la variable à l’aide de [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Si la valeur est un type simple, l’applet de commande récupère le même type. S’il s’agit d’un type complexe, le type `PSCustomObject` est récupéré.

>[!NOTE]
>Un script PowerShell ne peut pas récupérer une valeur chiffrée. La seule façon de procéder consiste à utiliser une activité `Get-AutomationVariable` dans un runbook ou une configuration DSC.

L’exemple suivant montre comment créer une variable de type chaîne, puis retourner sa valeur.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

L’exemple suivant montre comment créer une variable de type complexe, puis récupérer ses propriétés. Dans ce cas, un objet de machine virtuelle issu de [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) est utilisé.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Créer et utiliser une variable dans un runbook ou une configuration DSC

La seule façon de créer une variable à partir d’un runbook ou d’une configuration DSC consiste à utiliser l’applet de commande `New-AzAutomationVariable` ou son équivalent dans le module AzureRM. Le script utilise cette applet de commande pour définir la valeur initiale de la variable. Le script peut ensuite récupérer la valeur à l’aide de `Get-AzAutomationVariable`. Si la valeur est un type simple, ce même type est récupéré. S’il s’agit d’un type complexe, le type `PSCustomObject` est récupéré.

>[!NOTE]
>La seule manière de récupérer une valeur chiffrée consiste à utiliser l’activité `Get-AutomationVariable` dans un runbook ou une configuration DSC. 

### <a name="textual-runbook-samples"></a>Exemple de Runbooks textuels

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Définir et récupérer une valeur simple à partir d’une variable

Les exemples de commandes suivants montrent comment définir et récupérer une variable dans un Runbook textuel. Cet exemple suppose la création de variables d’entiers nommées `NumberOfIterations` et `NumberOfRunnings`, et d’une variable de chaîne nommée `SampleMessage`.

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Définir et récupérer une variable dans un runbook Python 2

L’exemple suivant montre comment utiliser une variable, définir une variable et gérer une exception pour une variable inexistante dans un runbook Python 2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Exemples de Runbook graphiques

Dans un runbook graphique, vous pouvez ajouter l’activité `Get-AutomationVariable` ou `Set-AutomationVariable`. Il vous suffit de cliquer avec le bouton droit sur la variable dans le volet Bibliothèque de l’éditeur graphique et de sélectionner l’activité souhaitée.

![Ajouter une variable à la zone de dessin](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Définir des valeurs dans une variable

L'image suivante montre des exemples d'activité pour mettre à jour une variable avec une valeur simple dans un Runbook graphique. Dans cet exemple, `Get-AzVM` récupère une seule machine virtuelle Azure et enregistre le nom d’ordinateur dans une variable de chaîne Automation existante. Peu importe si le [lien est un pipeline ou une séquence](../automation-graphical-authoring-intro.md#links-and-workflow), car le code n’attend qu’un objet unique dans la sortie.

![Définir une variable simple](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la connexion d’activités lors de la création graphique, consultez [Liens lors de la création graphique](../automation-graphical-authoring-intro.md#links-and-workflow).
- Pour une prise en main des runbooks graphiques, voir [Mon premier runbook graphique](../automation-first-runbook-graphical.md).
