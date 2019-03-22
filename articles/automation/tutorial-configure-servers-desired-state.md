---
title: Configurer des serveurs à l’état souhaité et gérer la dérive avec Azure Automation
description: 'Tutoriel : Gérer les configurations de serveur avec Azure Automation State Configuration'
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 582533d23757de748b9cc7d40e45acc00240d384
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570315"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurer les serveurs à l’état souhaité et gérer la dérive

Azure Automation State Configuration vous permet de spécifier des configurations pour vos serveurs et de vérifier que leur état ainsi défini perdure dans le temps.

> [!div class="checklist"]
> - Intégrer une machine virtuelle à gérer par Azure Automation DSC
> - Charger une configuration dans Azure Automation
> - Compiler une configuration dans une configuration de nœud
> - Attribuer une configuration de nœud à un nœud géré
> - Vérifier l’état de conformité d’un nœud géré

## <a name="prerequisites"></a>Conditions préalables

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Un compte Azure Automation. Pour obtenir des instructions sur la création d’un compte d’identification Azure Automation, consultez [Authentifier des Runbooks avec un compte d’identification Azure](automation-sec-configure-azure-runas-account.md).
- Une machine virtuelle Azure Resource Manager (non classique) exécutant Windows Server 2008 R2 ou version ultérieure. Pour obtenir des instructions sur la création d’une machine virtuelle, consultez [Créer votre première machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Module Azure PowerShell, version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Des notions de base de la configuration de l’état souhaité. Pour en savoir plus sur DSC, consultez [Vue d’ensemble de la fonctionnalité Desired State Configuration de Windows PowerShell](https://docs.microsoft.com/powershell/dsc/overview).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Créer et charger une configuration dans Azure Automation

Pour ce didacticiel, nous allons utiliser une configuration DSC simple qui permet de s’assurer qu’IIS est installé sur la machine virtuelle.

Pour obtenir des informations sur les configurations DSC, consultez [Configurations DSC](/powershell/dsc/configurations).

Dans un éditeur de texte, tapez la commande suivante et enregistrez-la localement sous `TestConfig.ps1`.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

Appelez l’applet de commande `Import-AzureRmAutomationDscConfiguration` pour charger la configuration dans votre compte Automation :

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compiler une configuration dans une configuration de nœud

Une configuration DSC doit être compilée dans une configuration de nœud avant de pouvoir être affectée à un nœud.

Pour obtenir des informations sur la compilation des configurations, consultez [Configurations DSC](/powershell/dsc/configurations).

Appelez l’applet de commande `Start-AzureRmAutomationDscCompilationJob` pour compiler la configuration `TestConfig` dans une configuration de nœud :

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Une configuration de nœud nommée `TestConfig.WebServer` est créée dans votre compte Automation.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Inscrire une machine virtuelle devant être gérée par State Configuration

Azure Automation State Configuration vous permet de gérer vos machines virtuelles Azure (par le biais des modèles de déploiement classique et Resource Manager), vos machines virtuelles locales, vos machines Linux, vos machines virtuelles AWS et vos ordinateurs physiques en local. Dans cette rubrique, nous allons voir comment inscrire uniquement des machines virtuelles Azure Resource Manager. Pour plus d’informations sur l’inscription d’autres types de machines, consultez [Intégration de machines pour la gestion avec Azure Automation State Configuration](automation-dsc-onboarding.md).

Appelez l’applet de commande `Register-AzureRmAutomationDscNode` pour inscrire votre machine virtuelle auprès d’Azure Automation State Configuration.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

La machine virtuelle spécifiée est inscrite comme nœud géré dans State Configuration.

### <a name="specify-configuration-mode-settings"></a>Spécifier les paramètres du mode de configuration

Lorsque vous inscrivez une machine virtuelle comme nœud géré, vous pouvez également indiquer les propriétés de la configuration. Par exemple, vous pouvez spécifier que l’état de la machine ne doit être appliqué qu’une seule fois (DSC n’essaie pas d’appliquer la configuration après la vérification initiale) en indiquant `ApplyOnly` comme valeur de la propriété **ConfigurationMode** :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Vous pouvez également préciser la fréquence à laquelle DSC vérifie l’état de configuration à l’aide de la propriété **ConfigurationModeFrequencyMins** :

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Pour plus d’informations sur la définition des propriétés de configuration d’un nœud géré, consultez [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Pour plus d’informations sur les paramètres de configuration DSC, consultez [Configuration du Gestionnaire de configuration local](/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Attribuer une configuration de nœud à un nœud géré

Nous pouvons désormais attribuer la configuration de nœud compilé à la machine virtuelle que nous souhaitons configurer.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Cette opération attribue la configuration de nœud nommée `TestConfig.WebServer` au nœud DSC inscrit nommé `DscVm`.
Par défaut, le nœud DSC est vérifié pour la conformité avec la configuration de nœud toutes les 30 minutes.
Pour obtenir des informations sur la modification de l’intervalle de vérification de conformité, consultez [Configuration du Gestionnaire de configuration local](/PowerShell/DSC/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Vérifier l’état de conformité d’un nœud géré

Vous pouvez obtenir des rapports sur l’état de conformité d’un nœud géré en appelant l’applet de commande `Get-AzureRmAutomationDscNodeReport` :

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer, consultez [Prise en main d’Azure Automation State Configuration](automation-dsc-getting-started.md)
- Pour savoir comment intégrer des nœuds, consultez [Intégration de machines pour la gestion avec Azure Automation State Configuration](automation-dsc-onboarding.md)
- Pour savoir comment compiler des configurations DSC pour les affecter à des nœuds cibles, consultez [Compilation de configurations dans Azure Automation State Configuration](automation-dsc-compile.md).
- Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Applets de commande d’Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Pour obtenir des informations sur les prix, consultez [Tarification d’Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Pour voir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md)
