---
title: Déployer un modèle Azure Resource Manager dans un runbook PowerShell Azure Automation
description: Cet article explique comment déployer un modèle Azure Resource Manager stocké dans Stockage Azure à partir d’un runbook PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8dd1c0269b9211900a5532e7a0d9e7d119830f68
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265628"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-automation-powershell-runbook"></a>Déployer un modèle Azure Resource Manager dans un runbook Automation PowerShell

Vous pouvez écrire un [runbook Automation PowerShell](./learn/powershell-runbook-managed-identity.md) qui déploie une ressource Azure en utilisant un [modèle Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Les modèles vous permettent d’utiliser Azure Automation pour automatiser le déploiement de vos ressources Azure. Vous pouvez gérer vos modèles Resource Manager dans un emplacement central et sécurisé tel que le Stockage Azure.

Dans cet article, nous créons un runbook PowerShell qui utilise un modèle Resource Manager stocké dans le service [Stockage Azure](../storage/common/storage-introduction.md) pour déployer un nouveau compte de stockage Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Un compte Azure Automation avec au moins une identité managée affectée par l’utilisateur. Pour plus d’informations, consultez [Utilisation d’une identité managée affectée par l’utilisateur pour un compte Azure Automation](./add-user-assigned-identity.md).

* Modules Az : `Az.Accounts`, `Az.ManagedServiceIdentity`, `Az.Resources` et `Az.Storage`. Importés dans le compte Automation. Pour plus d’informations, consultez [Importer des modules Az](./shared-resources/modules.md#import-az-modules).

* [Compte de stockage Azure](../storage/common/storage-account-create.md) dans lequel stocker le modèle Resource Manager.

* Azure PowerShell installé sur un ordinateur local. Pour plus d'informations sur l'obtention d'Azure PowerShell, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Vous aurez également besoin du module [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity). `Az.ManagedServiceIdentity` est un module en préversion et n’est pas installé dans le cadre du module Az. Pour l’installer, exécutez `Install-Module -Name Az.ManagedServiceIdentity`.

## <a name="assign-permissions-to-managed-identities"></a>Attribuer des autorisations aux identités managées

Attribuez des autorisations aux identités managées pour effectuer les tâches liées au stockage dans le runbook.

1. Connectez-vous à Azure de manière interactive à l’aide de la cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) et suivez les instructions.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. Fournissez une valeur appropriée pour les variables ci-dessous, puis exécutez le script.

    ```powershell
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $storageAccount = "storageAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    $storageTemplate = "path\storageTemplate.json"
    $runbookScript = "path\runbookScript.ps1"
    ```

1. Attribuez le rôle `reader` à l’identité managée affectée par le système pour exécuter la cmdlet `Get-AzUserAssignedIdentity`.

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. Attribuez le rôle `Storage Account Contributor` à l’identité managée affectée par l’utilisateur pour les actions sur le compte de stockage.

    ```powershell
    $UAMI_ID = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI_ID `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Storage Account Contributor"
    ```


## <a name="create-the-resource-manager-template"></a>Créer le modèle Resource Manager

Dans cet exemple, vous utilisez un modèle Resource Manager qui déploie un nouveau compte de stockage Azure. Créez un fichier local appelé `storageTemplate.json` et collez-y le code suivant :

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

## <a name="save-the-resource-manager-template-in-azure-files"></a>Enregistrer le modèle Resource Manager dans Stockage Azure

Utilisez PowerShell pour créer un partage de fichiers Azure et chargez-y `storageTemplate.json`. Pour obtenir des instructions sur la création d’un partage de fichier et sur le chargement d’un fichier sur le portail Azure, consultez [Bien démarrer avec le stockage de fichiers Azure sur Windows](../storage/files/storage-files-quick-create-use-windows.md).

Exécutez les commandes suivantes pour créer un partage de fichiers et y charger le modèle Resource Manager.

```powershell
# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName $resourceGroup -Name $storageAccount

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the storageTemplate.json file to the new file share
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $storageTemplate
```

## <a name="create-the-powershell-runbook-script"></a>Créer le script de runbook PowerShell

Créez un script PowerShell qui récupère le fichier `storageTemplate.json` auprès de Stockage Azure et déploie le modèle pour créer un nouveau compte de stockage Azure.  Créez un fichier local appelé `runbookScript.ps1` et collez-y le code suivant :


```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $resourceGroup,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccount,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $storageFileName,

    [Parameter(Mandatory=$true)]
    [string]
    $userAssignedManagedIdentity
)

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with user-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context
$identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup `
    -Name $userAssignedManagedIdentity `
    -DefaultProfile $AzureContext
$AzureContext = (Connect-AzAccount -Identity -AccountId $identity.ClientId).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageAccountKey

Get-AzStorageFileContent `
    -ShareName 'resource-templates' `
    -Context $Context `
    -path 'storageTemplate.json' `
    -Destination 'C:\Temp' -Force

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $storageFileName

# Deploy the storage account
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroup `
    -TemplateFile $TemplateFile `
    -TemplateParameterObject $Parameters 
```

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importer et publier le runbook dans votre compte Azure Automation

Utilisez PowerShell pour importer le runbook dans votre compte Automation, puis publiez le runbook. Pour plus d’informations sur l’importation et la publication de runbooks sur le portail Azure, consultez [Gérer des runbooks dans Azure Automation](manage-runbooks.md).

Pour importer `runbookScript.ps1` dans votre compte Automation en tant que runbook PowerShell, exécutez les commandes PowerShell suivantes :

```powershell
$importParams = @{
    Path = $runbookScript
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Type = "PowerShell"
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Démarrer le runbook

Nous allons maintenant démarrer le runbook en appelant la cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Pour plus d’informations sur la façon de démarrer un runbook dans le portail Azure, consultez [Démarrage d’un Runbook dans Azure Automation](./start-runbooks.md).

Dans la console PowerShell, exécutez les commandes suivantes :

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    resourceGroup = $resourceGroup
    storageAccount = $storageAccount
    storageAccountKey = $key[0].Value # We got this key earlier
    storageFileName = "storageTemplate.json"
    userAssignedManagedIdentity = $userAssignedManagedIdentity
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    resourceGroup = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Une fois que le runbook s’exécute, vous pouvez vérifier son état en extrayant la valeur de propriété de l’objet de traitement `$job.Status`.

Le runbook récupère le modèle Resource Manager et l’utilise pour déployer un nouveau compte de stockage Azure. Vous pouvez voir que le nouveau compte de stockage a été créé en exécutant la commande suivante :

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les modèles Resource Manager, consultez [Vue d'ensemble d'Azure Resource Manager](../azure-resource-manager/management/overview.md).
* Pour démarrer avec Stockage Azure, consultez [Introduction à Azure Storage](../storage/common/storage-introduction.md).
* Pour trouver d’autres runbooks Azure Automation utiles, consultez [Utiliser des runbooks et des modules dans Azure Automation](automation-runbook-gallery.md).