---
title: Gérer les variables dans Azure Automation
description: Les ressources de variables sont des valeurs disponibles pour tous les Runbooks et configurations DSC d’Azure Automation.  Cet article présente les variables de façon détaillée et comment les utiliser dans une création textuelle ou graphique.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bf7840daad02f679cad4c3b798d2add02c863a15
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82651962"
---
# <a name="manage-variables-in-azure-automation"></a>Gérer les variables dans Azure Automation

Les ressources de variables sont des valeurs disponibles pour tous les runbooks et configurations DSC de votre compte Automation. Vous pouvez les gérer à partir du portail Azure, à partir de PowerShell, au sein d’un runbook ou dans une configuration DSC.

Les variables Automation sont utiles pour les scénarios suivants :

- Partage d’une valeur entre plusieurs runbooks ou configurations DSC.

- Partage d’une valeur entre plusieurs tâches du même runbook ou de la même configuration DSC.

- Gestion d’une valeur utilisée par des runbooks ou des configurations DSC à partir du portail ou de la ligne de commande PowerShell. Un ensemble d’éléments de configuration communs, comme la liste spécifique des noms de machines virtuelles, un groupe de ressources spécifique, un nom de domaine Active Directory, etc, constitue un exemple.  

Azure Automation conserve les variables et les rend disponibles même en cas d’échec d’une configuration runbook ou DSC. Ce comportement permet à un runbook ou à une configuration DSC de définir une valeur par la suite utilisée par un autre runbook, ou par le même runbook ou la même configuration DSC lors de son exécution suivante.

Azure Automation stocke chaque variable chiffrée de manière sécurisée. Lorsque vous créez une variable, vous pouvez spécifier son mode de chiffrement et de stockage par Azure Automation sous la forme d’une ressource sécurisée. 

>[!NOTE]
>Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation en utilisant une clé unique générée pour chaque compte Automation. Azure Automation stocke la clé dans le coffre de clés managé par le système. Avant de stocker une ressource sécurisée, Automation charge la clé à partir de Key Vault, puis l’utilise pour chiffrer la ressource. 

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Types de variables

Lorsque vous créez une variable avec le portail Azure, vous devez spécifier un type de données dans la liste déroulante afin que le portail puisse afficher le contrôle approprié pour l’entrée de la valeur de la variable. Voici les types de variables disponibles dans Azure Automation :

* String
* Integer
* DateTime
* Boolean
* Null

La variable n’est pas limitée au type de données spécifié. Vous devez définir la variable à l'aide de Windows PowerShell si vous souhaitez spécifier une valeur d'un type différent. Si vous indiquez `Not defined`, la valeur de la variable est définie sur Null. Vous devez définir la valeur avec l’applet de commande [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) ou l’applet de commande interne `Set-AutomationVariable`.

Vous ne pouvez pas utiliser le portail Azure pour créer ou modifier la valeur d’un type de variable complexe. Toutefois, vous pouvez fournir une valeur de n’importe quel type à l’aide de Windows PowerShell. Les types complexes sont récupérés en tant que [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Vous pouvez stocker plusieurs valeurs dans une seule variable en créant un tableau ou une table de hachage, et en l'enregistrant sur la variable.

>[!NOTE]
>Les variables de nom de machine virtuelle ne peuvent pas dépasser 80 caractères. Les variables de groupe de ressources ne peuvent pas dépasser 90 caractères. Voir [Règles de nommage et restrictions pour les ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules).

## <a name="powershell-cmdlets-to-access-variables"></a>Applets de commande PowerShell pour accéder aux variables

Les applets de commande du tableau suivant créent et gèrent les variables Automation avec Windows PowerShell. Elles sont fournies dans le cadre des [modules Az](modules.md#az-modules).

| Applet de commande | Description |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Récupère la valeur d'une variable existante. Si la valeur est un type simple, ce même type est récupéré. S’il s’agit d’un type complexe, le type `PSCustomObject` est récupéré. <br>**Remarque :**  Vous ne pouvez pas utiliser cette applet de commande pour récupérer la valeur d’une variable chiffrée. La seule façon de procéder consiste à utiliser l’applet de commande interne `Get-AutomationVariable` dans un runbook ou une configuration DSC. Consultez [Applets de commande internes pour accéder aux variables](#internal-cmdlets-to-access-variables). |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Crée une variable et définit sa valeur.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Supprime une variable existante.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Définit la valeur d'une variable existante. |

## <a name="internal-cmdlets-to-access-variables"></a>Applets de commande internes pour accéder aux variables

Les applets de commande internes figurant dans le tableau suivant sont utilisées pour accéder aux variables dans vos runbooks et configurations DSC. Ces applets de commande sont fournies avec le module global `Orchestrator.AssetManagement.Cmdlets`. Pour plus d’informations, consultez [Applets de commande interne](modules.md#internal-cmdlets).

| Applet de commande interne | Description |
|:---|:---|
|`Get-AutomationVariable`|Récupère la valeur d'une variable existante.|
|`Set-AutomationVariable`|Définit la valeur d'une variable existante.|

> [!NOTE]
> Évitez d’utiliser des variables dans le paramètre `Name` de `Get-AutomationVariable` dans un runbook ou une configuration DSC. L’utilisation des variables peut compliquer la détection des dépendances entre les runbooks et les variables Automation au moment de la conception.

`Get-AutomationVariable` ne fonctionne pas dans PowerShell, mais uniquement dans un runbook ou une configuration DSC. Par exemple, pour voir la valeur d’une variable chiffrée, vous pouvez créer un runbook pour obtenir cette variable, puis l’écrire dans le flux de sortie :
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>Fonctions Python 2 pour accéder aux variables

Les fonctions du tableau suivant sont utilisées pour accéder aux variables d’un runbook Python 2.

|Fonctions Python 2|Description|
|:---|:---|
|`automationassets.get_automation_variable`|Récupère la valeur d'une variable existante. |
|`automationassets.set_automation_variable`|Définit la valeur d'une variable existante. |

> [!NOTE]
> Vous devez importer le module `automationassets` en haut de votre runbook Python pour accéder aux fonctions des ressources.

## <a name="create-and-get-a-variable"></a>Créer et obtenir une variable

>[!NOTE]
>Si vous voulez supprimer le chiffrement d’une variable, vous devez supprimer la variable et la recréer non chiffrée.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Créer et obtenir une variable à l’aide du portail Azure

1. À partir de votre compte Automation, cliquez sur la vignette **Ressources** puis, dans le panneau **Ressources**, sélectionnez **Variables**.
2. Sur la vignette **Variables**, sélectionnez **Ajouter une variable**.
3. Définissez les options dans le panneau **Nouvelle variable** et cliquez sur **Créer** pour enregistrer la nouvelle variable.

> [!NOTE]
> Une fois que vous avez enregistré une variable chiffrée, vous ne pouvez plus la voir dans le portail. Elle peut uniquement être mise à jour.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Créer et obtenir une variable dans Windows PowerShell

Votre runbook ou votre configuration DSC utilise l’applet de commande `New-AzAutomationVariable` pour créer une variable et définir sa valeur initiale. Si la variable est chiffrée, l’appel doit utiliser le paramètre `Encrypted`. Votre script peut récupérer la valeur de la variable avec `Get-AzAutomationVariable`. 

>[!NOTE]
>Un script PowerShell ne peut pas récupérer une valeur chiffrée. La seule façon d’y parvenir consiste à utiliser l’applet de commande interne `Get-AutomationVariable`.

L’exemple suivant montre comment créer une variable de chaîne, puis retourner sa valeur.

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

## <a name="textual-runbook-examples"></a>Exemples de runbook textuel

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>Récupérer et définir une valeur simple à partir d’une variable

Les exemples suivants montrent comment définir et récupérer une variable dans un runbook textuel. Cet exemple suppose la création de variables d’entiers nommées `NumberOfIterations` et `NumberOfRunnings`, et d’une variable de chaîne nommée `SampleMessage`.

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

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Récupérer et définir une variable dans un runbook Python 2

L’exemple suivant montre comment obtenir une variable, définir une variable et gérer une exception pour une variable inexistante dans un runbook Python 2.

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

## <a name="graphical-runbook-examples"></a>Exemples de runbook graphique

Dans un runbook graphique, vous pouvez ajouter des activités pour les applets de commande internes `Get-AutomationVariable` ou `Set-AutomationVariable`. Il vous suffit de cliquer avec le bouton droit sur chaque variable dans le volet Bibliothèque de l’éditeur graphique, puis de sélectionner l’activité souhaitée.

![Ajouter une variable à la zone de dessin](../media/variables/runbook-variable-add-canvas.png)

L’image suivante montre des exemples d’activité pour mettre à jour une variable avec une valeur simple dans un runbook graphique. Dans cet exemple, l’activité de `Get-AzVM` récupère une seule machine virtuelle Azure et enregistre le nom d’ordinateur dans une variable de chaîne Automation existante. Peu importe si le [lien est un pipeline ou une séquence](../automation-graphical-authoring-intro.md#links-and-workflow), car le code n’attend qu’un objet unique dans la sortie.

![Définir une variable simple](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les applets de commande utilisées pour accéder aux variables, consultez [Gérer les modules dans Azure Automation](modules.md).
* Pour obtenir des informations générales sur les runbooks, consultez [Exécution d’un runbook dans Azure Automation](../automation-runbook-execution.md).
* Pour obtenir des informations détaillées sur les configurations DSC, consultez [Présentation de State Configuration](../automation-dsc-overview.md).
