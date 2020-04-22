---
title: Déployer un modèle Azure Resource Manager dans un runbook Azure Automation
description: Comment déployer un modèle Azure Resource Manager stocké dans Stockage Azure à partir d’un runbook
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powerShell, runbook, json, azure automation
ms.openlocfilehash: 2a6652c988eb77a1c5c7dbf800586b1c5fb756c4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392209"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Déployer un modèle Azure Resource Manager dans un runbook PowerShell Azure Automation

Vous pouvez écrire un [runbook PowerShell Azure Automation](automation-first-runbook-textual-powershell.md) qui déploie une ressource Azure en utilisant un [modèle Azure Resource Manager](../azure-resource-manager/resource-manager-create-first-template.md).

Ainsi, vous pouvez automatiser le déploiement de ressources Azure. Vous pouvez gérer vos modèles Resource Manager à un emplacement central et sécurisé, tel que Stockage Azure.

Dans cet article, nous créons un runbook PowerShell qui utilise un modèle Resource Manager stocké dans le service [Stockage Azure](../storage/common/storage-introduction.md) pour déployer un nouveau compte de stockage Azure.

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prérequis

Pour ce tutoriel, vous devez disposer des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [créer un compte gratuit](https://azure.microsoft.com/free/).
* [compte Automation](automation-sec-configure-azure-runas-account.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure.  Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* [Compte de stockage Azure](../storage/common/storage-create-storage-account.md) dans lequel stocker le modèle Resource Manager
* Azure PowerShell installé sur un ordinateur local. Pour plus d'informations sur l'obtention d'Azure PowerShell, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0).

## <a name="create-the-resource-manager-template"></a>Créer le modèle Resource Manager

Pour cet exemple, nous utilisons un modèle Resource Manager qui déploie un nouveau compte de stockage Azure.

Dans un éditeur de texte, copiez le texte suivant :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Enregistrez le fichier localement sous le nom **TemplateTest.json**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Enregistrer le modèle Resource Manager dans Stockage Azure

Nous allons maintenant utiliser PowerShell pour créer un partage de fichiers Stockage Azure et charger le fichier **TemplateTest.json**.
Pour obtenir des instructions sur la création d’un partage de fichier et sur le chargement d’un fichier sur le portail Azure, consultez [Bien démarrer avec le stockage de fichiers Azure sur Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Lancez PowerShell sur votre ordinateur local et exécutez les commandes suivantes pour créer un partage de fichiers et y charger le modèle Resource Manager.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Créer le script de runbook PowerShell

Nous allons maintenant créer un script PowerShell permettant de récupérer le fichier **TemplateTest.json** à partir de Stockage Azure et de déployer le modèle pour créer un compte de stockage Azure.

Dans un éditeur de texte, collez le texte suivant :

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Enregistrez le fichier localement sous le nom **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importer et publier le runbook dans votre compte Azure Automation

Nous allons maintenant utiliser PowerShell pour importer le runbook dans votre compte Azure Automation, puis publier le runbook. Pour plus d'informations sur l'importation et la publication d'un runbook sur le portail Azure, consultez [Gérer des runbooks dans Azure Automation](manage-runbooks.md).

Pour importer **DeployTemplate.ps1** sur votre compte Automation en tant que runbook PowerShell, exécutez les commandes PowerShell suivantes :

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Démarrer le runbook

Nous allons maintenant démarrer le runbook en appelant la cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
). Pour plus d’informations sur la façon de démarrer un runbook dans le portail Azure, consultez [Démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md).

Dans la console PowerShell, exécutez les commandes suivantes :

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Le runbook s’exécute, et vous pouvez vérifier son état en exécutant `$job.Status`.

Le runbook récupère le modèle Resource Manager et l’utilise pour déployer un nouveau compte de stockage Azure.
Vous pouvez voir que le nouveau compte de stockage a été créé en exécutant la commande suivante :

```powershell
Get-AzStorageAccount
```

## <a name="summary"></a>Résumé

Et voilà ! Vous pouvez maintenant utiliser Azure Automation et Stockage Azure avec des modèles Resource Manager pour déployer toutes vos ressources Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les modèles Resource Manager, consultez [Vue d'ensemble d'Azure Resource Manager](../azure-resource-manager/management/overview.md).
* Pour démarrer avec Stockage Azure, consultez [Introduction à Azure Storage](../storage/common/storage-introduction.md).
* Pour rechercher d’autres runbooks Azure Automation utiles, consultez [Galeries de runbooks et de modules pour Azure Automation](automation-runbook-gallery.md).
* Pour accéder à d'autres modèles Resource Manager utiles, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/).
* Pour obtenir des informations de référence sur les cmdlets PowerShell, consultez [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
