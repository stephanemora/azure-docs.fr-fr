---
title: Déployer des ressources avec le modèle et PowerShell | Microsoft Docs
description: Utilisez Azure Resource Manager et Azure PowerShell pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: daeff897cf284df6e820afbcdd35ee54bf88db08
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405400"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell

Apprenez à utiliser Azure PowerShell avec des modèles Resource Manager pour déployer vos ressources dans Azure. Pour plus d'informations sur les concepts de déploiement et de gestion des solutions Azure, consultez [Vue d'ensemble d'Azure Resource Manager](resource-group-overview.md).

Le déploiement d'un modèle s'effectue généralement en deux étapes :

1. Créez un groupe de ressources. Le groupe de ressources sert de conteneur pour les ressources déployées. Le nom du groupe de ressources ne peut contenir que des caractères alphanumériques, des points, des traits de soulignement, des traits d'union et des parenthèses. Il peut comprendre jusqu’à 90 caractères. Il ne peut pas se terminer par un point.
2. Déployez un modèle. Le modèle définit les ressources à créer.  Le déploiement crée les ressources dans le groupe de ressources spécifié.

Cet article utilise cette méthode de déploiement en deux étapes.  L'autre option consiste à déployer un groupe de ressources et les ressources en même temps.  Pour plus d'informations, consultez [Créer un groupe de ressources et déployer des ressources](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="prerequisites"></a>Conditions préalables

À moins d'utiliser [Azure Cloud Shell](#deploy-templates-from-azure-cloud-shell) pour déployer les modèles, vous devez installer Azure PowerShell et vous connecter à Azure :
- **Installez les cmdlets Azure PowerShell sur votre ordinateur local.** Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](/powershell/azure/get-started-azureps).
- **Connectez-vous à Azure à l'aide de [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Si vous disposez de plusieurs abonnements Azure, vous devrez peut-être également exécuter [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Pour plus d'informations, consultez [Utiliser plusieurs abonnements Azure](/powershell/azure/manage-subscriptions-azureps).
- * Téléchargez et enregistrez un [modèle de démarrage rapide](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Le nom du fichier local utilisé dans cet article est **c:\MyTemplates\azuredeploy.json**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-templates-stored-locally"></a>Déployer des modèles stockés localement

L’exemple suivant crée un groupe de ressources et déploie un modèle à partir de votre ordinateur local :

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Remarque : *c:\MyTemplates\azuredeploy.json* est un modèle de démarrage rapide.  Consultez les [Conditions préalables](#prerequisites).

Le déploiement peut prendre plusieurs minutes.

## <a name="deploy-templates-stored-externally"></a>Déployer des modèles stockés en externe

Au lieu de stocker les modèles Resource Manager sur votre ordinateur local, vous pouvez les stocker dans un emplacement externe. Vous pouvez stocker des modèles dans un dépôt de contrôle de code source (par exemple, GitHub). Vous pouvez aussi les stocker dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation.

Pour déployer un modèle externe, utilisez le paramètre **TemplateUri**. Pour déployer l’exemple de modèle à partir de GitHub, utilisez l’URI figurant dans l’exemple.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

L’exemple précédent nécessite un URI accessible publiquement pour le modèle, ce qui convient pour la plupart des scénarios, sachant que votre modèle ne doit pas inclure de données sensibles. Si vous avez besoin de spécifier des données sensibles (par exemple, un mot de passe d’administrateur), passez cette valeur en tant que paramètre sécurisé. Toutefois, si vous ne souhaitez pas que votre modèle soit accessible au public, vous pouvez le protéger en le stockant dans un conteneur de stockage privé. Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton de signature d’accès partagé (SAS), consultez [Déployer un modèle privé avec un jeton SAS](resource-manager-powershell-sas-token.md). Pour suivre un tutoriel, consultez [Tutoriel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-templates-from-azure-cloud-shell"></a>Déployer des modèles à partir d'Azure Cloud Shell

Vous pouvez utiliser [Azure Cloud Shell](https://shell.azure.com) pour déployer votre modèle. Pour déployer un modèle externe, fournissez son URI. Pour déployer un modèle local, vous devez d’abord charger votre modèle dans le compte de stockage de votre Cloud Shell. Pour charger des fichiers dans Azure Cloud Shell, sélectionnez l'icône de menu **Charger/télécharger des fichiers** de la fenêtre d'Azure Cloud Shell.

Pour ouvrir Azure Cloud Shell, accédez à [https://shell.azure.com](https://shell.azure.com), ou sélectionnez **Essayer** dans la section de code suivante :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Pour coller le code, cliquez sur celui-ci avec le bouton droit et sélectionnez **Coller**.

## <a name="deploy-to-multiple-resource-groups-or-subscriptions"></a>Déployer sur plusieurs groupes de ressources ou abonnements

En général, vous déployez toutes les ressources dans votre modèle sur un seul groupe de ressources. Toutefois, il existe des scénarios dans lesquels vous pouvez souhaitez déployer un ensemble de ressources, tout en les plaçant dans différents groupes de ressources ou abonnements. Vous ne pouvez pas déployer sur plus de cinq groupes de ressources dans un déploiement. Pour plus d'informations, consultez [Déployer des ressources Azure dans différents groupes de ressources et abonnements](resource-manager-cross-resource-group-deployment.md).

## <a name="redeploy-when-deployment-fails"></a>Redéploiement en cas d’échec du déploiement

En cas d'échec du déploiement, vous pouvez automatiquement relancer un déploiement antérieur réussi à partir de votre historique de déploiement. Pour spécifier le redéploiement, utilisez le paramètre `-RollbackToLastDeployment` ou `-RollBackDeploymentName` dans la commande de déploiement.

Pour utiliser cette option, vos déploiements doivent avoir des noms uniques afin de pouvoir être identifiés dans l’historique. Si les noms ne sont pas uniques, le déploiement actuellement en échec peut remplacer le déploiement réussi précédemment dans l’historique. Vous pouvez uniquement utiliser cette option avec les déploiements de niveau racine. Les déploiements à partir d’un modèle imbriqué ne sont pas disponibles pour le redéploiement.

Pour redéployer le dernier déploiement réussi, ajoutez le paramètre `-RollbackToLastDeployment` comme indicateur.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Pour redéployer un déploiement spécifique, utilisez le paramètre `-RollBackDeploymentName` et indiquez le nom du déploiement.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

Le déploiement spécifié doit avoir réussi.

## <a name="pass-parameter-values"></a>Passer les valeurs de paramètre

Pour passer les valeurs de paramètre, vous pouvez utiliser des paramètres inline ou un fichier de paramètres. Les exemples précédents dans cet article décrivent des paramètres inline.

### <a name="inline-parameters"></a>Paramètres inline

Pour passer les paramètres inline, indiquez les noms des paramètres au moyen de la commande `New-AzResourceGroupDeployment`. Par exemple, pour passer une chaîne et un tableau à un modèle, utilisez :

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Vous pouvez également récupérer le contenu d’un fichier et fournir ce contenu en tant que paramètre inline.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Obtenir une valeur de paramètre à partir d’un fichier est utile lorsque vous devez fournir des valeurs de configuration. Par exemple, vous pouvez fournir des [valeurs cloud-init pour une machine virtuelle Linux](../virtual-machines/linux/using-cloud-init.md).

### <a name="parameter-files"></a>Fichiers de paramètres

Au lieu de passer des paramètres en tant que valeurs inline dans votre script, il peut s’avérer plus facile d’utiliser un fichier JSON qui contient les valeurs des paramètres. Le fichier de paramètres peut être un fichier local ou un fichier externe avec un URI accessible.

Le fichier de paramètres doit être au format suivant :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Notez que la section des paramètres comprend un nom de paramètre qui correspond au paramètre défini dans votre modèle (storageAccountType). Le fichier de paramètres contient une valeur pour le paramètre. Cette valeur est transmise automatiquement au modèle pendant le déploiement. Vous pouvez créer plusieurs fichiers de paramètres, puis transmettre le fichier de paramètres approprié pour le scénario.

Copiez l’exemple précédent et enregistrez-le dans un fichier nommé `storage.parameters.json`.

Pour transmettre un fichier de paramètres local, utilisez le paramètre **TemplateParameterFile** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Pour transmettre un fichier de paramètres externe, utilisez le paramètre **TemplateParameterUri** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>Priorité des paramètres

Vous pouvez utiliser des paramètres inline et un fichier de paramètres local pendant la même opération de déploiement. Par exemple, vous pouvez spécifier certaines valeurs dans le fichier de paramètres local et ajouter d’autres valeurs inline pendant le déploiement. Si vous fournissez des valeurs pour un paramètre à la fois dans le fichier de paramètres local et inline, la valeur inline est prioritaire.

Cependant, lorsque vous utilisez un fichier de paramètres externe, vous ne pouvez pas transmettre d’autres valeurs, qu’elles soient inline ou tirées d’un fichier local. Lorsque vous spécifiez un fichier de paramètres dans le paramètre **TemplateParameterUri**, tous les paramètres inline sont ignorés. Fournissez toutes les valeurs de paramètre dans le fichier externe. Si votre modèle inclut une valeur sensible que vous ne pouvez pas inclure dans le fichier de paramètres, ajoutez cette valeur à un coffre de clés, ou fournissez de manière dynamique toutes les valeurs de paramètre inline.

### <a name="parameter-name-conflicts"></a>Conflits de noms de paramètre

Si votre modèle inclut un paramètre utilisant le même nom que l’un des paramètres dans la commande PowerShell, PowerShell présente le paramètre de votre modèle avec le suffixe **FromTemplate**. Par exemple, un paramètre nommé **ResourceGroupName** dans votre modèle est en conflit avec le paramètre **ResourceGroupName** dans la cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En général, vous devez éviter cette confusion en ne nommant pas les paramètres avec un nom identique à celui des paramètres utilisés pour les opérations de déploiement.

## <a name="test-template-deployments"></a>Tester les déploiements de modèles

Pour tester votre modèle et vos valeurs de paramètre sans réellement déployer toutes les ressources, utilisez [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment).  

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Si aucune erreur n’est détectée, la commande se termine sans réponse. Si une erreur est détectée, la commande retourne un message d’erreur. Par exemple, la transmission d’une valeur incorrecte pour la référence (SKU) du compte de stockage retourne l’erreur suivante :

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Si votre modèle comporte une erreur de syntaxe, la commande retourne une erreur indiquant que le modèle n’a pas pu être analysé. Le message indique le numéro de ligne et la position de l’erreur d’analyse.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Étapes suivantes

- Pour déployer en toute sécurité votre service sur plusieurs régions, consultez [Azure Deployment Manager](deployment-manager-overview.md).
- Pour spécifier comment gérer les ressources présentes dans le groupe de ressources, mais non définies dans le modèle, consultez [Modes de déploiement Azure Resource Manager](deployment-modes.md).
- Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](resource-manager-powershell-sas-token.md).
