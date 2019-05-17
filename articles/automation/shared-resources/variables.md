---
title: Ressources de variables dans Azure Automation
description: Les ressources de variables sont des valeurs disponibles pour tous les Runbooks et configurations DSC d’Azure Automation.  Cet article présente les variables de façon détaillée et comment les utiliser dans une création textuelle ou graphique.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0ac34f1d1e7fc2a967c7608f31f3b943f9380d01
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786205"
---
# <a name="variable-assets-in-azure-automation"></a>Ressources de variables dans Azure Automation

Les ressources de variables sont des valeurs disponibles pour tous les Runbooks et configurations DSC de votre compte Automation. Ils peuvent être gérés à partir du portail Azure, PowerShell, d’un runbook ou la configuration de DSC. Les variables Automation sont utiles pour les scénarios suivants :

- Partager une valeur entre plusieurs Runbooks ou configurations DSC.

- Partager une valeur entre plusieurs tâches du même Runbook ou de la même configuration DSC.

- Gérer une valeur à partir du portail ou à partir de la ligne de commande PowerShell qui est utilisée par les runbooks ou configurations DSC, tel qu’un ensemble d’éléments de configuration communs comme une liste spécifique de noms de machine virtuelle, un groupe de ressources spécifique, un nom de domaine Active Directory et bien plus encore.  

Étant donné que les variables Automation sont conservées, elles sont disponibles même si le runbook ou la configuration de DSC échoue. Ce comportement permet à une valeur d’être définie par un runbook est ensuite utilisé par un autre, ou est utilisé par le même runbook ou d’une configuration DSC la prochaine fois qu’elle est exécutée.

Quand une variable est créée, vous pouvez spécifier qu’elle doit être stockée de manière chiffrée. Les variables chiffrées sont stockées de manière sécurisée dans Azure Automation, et sa valeur ne peut pas être récupérée à partir de la [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) applet de commande qui est fourni en tant que partie du module Azure PowerShell. Une valeur chiffrée ne peut être récupérée que d’une seule façon, à partir de l’activité **Get-AutomationVariable** d’un Runbook ou d’une configuration DSC.

>[!NOTE]
>Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l’aide d’une clé unique générée pour chaque compte Automation. Cette clé est stockée dans un coffre de clés managé par le système. Avant de stocker une ressource sécurisée, la clé est chargée à partir de Key Vault, puis utilisée pour chiffrer la ressource. Ce processus est managé par Azure Automation.

## <a name="variable-types"></a>Types de variables

Lorsque vous créez une variable avec le portail Azure, vous devez spécifier un type de données dans la liste déroulante afin que le portail puisse afficher le contrôle approprié pour l’entrée de la valeur de la variable. La variable n’est pas limitée à ce type de données. Vous devez définir la variable à l’aide de Windows PowerShell si vous souhaitez spécifier une valeur d’un type différent. Si vous spécifiez **non défini**, puis affecte la valeur de la variable **$null**, et vous devez définir la valeur avec le [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) applet de commande ou **Set-AutomationVariable** activité. Vous ne pouvez pas créer ou modifier la valeur pour un type de variable complexe dans le portail, mais vous pouvez fournir une valeur de tout type à l’aide de Windows PowerShell. Les types complexes sont retournés en tant que [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Vous pouvez stocker plusieurs valeurs dans une seule variable en créant un tableau ou une table de hachage, et en l'enregistrant sur la variable.

Voici la liste des types de variable disponibles dans Automation :

* Chaîne
* Integer
* DateTime
* Booléen
* Null

## <a name="azurerm-powershell-cmdlets"></a>Applets de commande AzureRM PowerShell

Pour AzureRM, les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources d’informations d’identification Automation avec Windows PowerShell. Elles font partie de la [module AzureRM.Automation](/powershell/azure/overview), qui est disponible pour une utilisation dans des runbooks Automation et les configurations DSC.

| Applets de commande | Description  |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Récupère la valeur d'une variable existante.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Crée une variable et définit sa valeur.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Supprime une variable existante.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Définit la valeur d'une variable existante.|

## <a name="activities"></a>Activités

Les activités dans le tableau suivant sont utilisées pour accéder aux informations d’identification dans un Runbook ou dans des configurations DSC.

| Activités | Description  |
|:---|:---|
|Get-AutomationVariable|Récupère la valeur d'une variable existante.|
|Set-AutomationVariable|Définit la valeur d'une variable existante.|

> [!NOTE]
> Évitez d’utiliser des variables dans le paramètre –Name de **Get-AutomationVariable** dans un Runbook ou une configuration DSC, car cela peut compliquer la découverte de dépendances entre les Runbooks ou la configuration DSC et les variables Automation au moment de la conception.

Les fonctions du tableau suivant sont utilisées pour accéder aux variables dans un runbook Python2 et les récupérer.

|Fonctions Python2|Description |
|:---|:---|
|automationassets.get_automation_variable|Récupère la valeur d'une variable existante. |
|automationassets.set_automation_variable|Définit la valeur d'une variable existante. |

> [!NOTE]
> Vous devez importer le module « automationassets » en haut de votre runbook Python afin d’accéder aux fonctions des ressources.

## <a name="creating-a-new-automation-variable"></a>Création d'une variable Automation

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Création d'une variable avec le portail Azure

1. À partir de votre compte Automation, cliquez sur la vignette **Ressources** puis, dans le panneau **Ressources**, sélectionnez **Variables**.
2. Sur la vignette **Variables**, sélectionnez **Ajouter une variable**.
3. Définissez les options dans le panneau **Nouvelle variable** et cliquez sur **Créer** pour enregistrer la nouvelle variable.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Pour créer une variable avec Windows PowerShell

L’applet de commande [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) crée une variable et définit sa valeur initiale. Vous pouvez récupérer la valeur en utilisant [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Si la valeur est un type simple, ce même type est retourné. S'il s'agit d'un type complexe, un **PSCustomObject** est retourné.

Les exemples de commandes suivants montrent comment créer une variable de type chaîne, puis retourner sa valeur.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Les exemples de commandes suivants montrent comment créer une variable de type complexe, puis retourner ses propriétés. Dans ce cas, une machine virtuelle à partir de **Get-AzureRmVm** est utilisée.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Utilisation d’une variable dans un Runbook ou une configuration DSC

Utilisez l’activité **Set-AutomationVariable** qui permet de définir la valeur d’une variable Automation dans un Runbook PowerShell ou dans une configuration DSC, et l’activité **Get-AutomationVariable** pour la récupérer. N’utilisez pas les applets de commande **Set-AzureRMAutomationVariable** et **Get-AzureRMAutomationVariable** dans un runbook ou une configuration DSC, car elles sont moins efficaces que les activités de flux de travail. Vous ne pouvez pas non plus récupérer la valeur des variables sécurisées avec **Get-AzureRMAutomationVariable**. La seule façon de créer une variable à partir d’un runbook ou d’une configuration DSC consiste à utiliser l’applet de commande [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable).

### <a name="textual-runbook-samples"></a>Exemple de Runbooks textuels

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Définition et récupération d'une valeur simple à partir d'une variable

Les exemples de commandes suivants montrent comment définir et récupérer une variable dans un Runbook textuel. Dans cet exemple, il est supposé que les variables de type entier nommées *NumberOfIterations* et *NumberOfRunnings* et une variable de type chaîne nommée *SampleMessage* ont été créé.

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Définition et récupération d’une variable dans Python2

L’exemple de code suivant montre comment utiliser une variable, définir une variable et gérer une exception pour une variable inexistante dans un runbook Python2.

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
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Exemples de Runbook graphiques

Dans un Runbook graphique, ajoutez l’activité **Get-AutomationVariable** ou **Set-AutomationVariable** en cliquant avec le bouton droit sur la variable dans le volet Bibliothèque de l’éditeur graphique et en sélectionnant l’activité souhaitée.

![Ajouter une variable à la zone de dessin](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Définition de valeurs dans une variable

L'image suivante montre des exemples d'activité pour mettre à jour une variable avec une valeur simple dans un Runbook graphique. Dans cet exemple, **Get-AzureRmVM** récupère une seule machine virtuelle Azure et le nom d’ordinateur enregistre dans une variable Automation existante avec un type de chaîne. Peu importe si le [lien est un pipeline ou une séquence](../automation-graphical-authoring-intro.md#links-and-workflow), car un seul objet est attendu dans la sortie.

![Définir une variable simple](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la connexion d’activités lors de la création graphique, consultez [Liens lors de la création graphique](../automation-graphical-authoring-intro.md#links-and-workflow)
- Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](../automation-first-runbook-graphical.md)
