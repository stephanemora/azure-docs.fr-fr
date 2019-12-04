---
title: Déployer des ressources avec PowerShell et un modèle
description: Utilisez Azure Resource Manager et Azure PowerShell pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: e726d1154fca1dbcce244783bf987bea6610cf98
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150704"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell

Apprenez à utiliser Azure PowerShell avec des modèles Resource Manager pour déployer vos ressources dans Azure. Pour plus d'informations sur les concepts de déploiement et de gestion des solutions Azure, consultez [Vue d'ensemble d'Azure Resource Manager](resource-group-overview.md).

## <a name="deployment-scope"></a>Étendue du déploiement

Vous pouvez cibler votre déploiement au niveau d’un abonnement Azure, ou d’un groupe de ressources dans un abonnement. Dans la plupart des cas, un déploiement cible un groupe de ressources. Effectuez des déploiements au niveau de l’abonnement pour appliquer des stratégies et des attributions de rôles dans tout l’abonnement. Utilisez également ce type de déploiement pour créer un groupe de ressources et y déployer des ressources. Les commandes à utiliser diffèrent en fonction de l’étendue du déploiement.

Pour un déploiement dans un **groupe de ressources**, utilisez la commande [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) :

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Pour un déploiement dans un **abonnement**, utilisez [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) :

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Pour plus d’informations sur les déploiements au niveau de l’abonnement, consultez [Créer des groupes de ressources et des ressources au niveau de l’abonnement](deploy-to-subscription.md).

Actuellement, les déploiements dans les groupes d’administration sont uniquement pris en charge avec l’API REST. Pour plus d’informations sur les déploiements au niveau du groupe d’administration, consultez [Créer des ressources au niveau du groupe d’administration](deploy-to-management-group.md).

Les exemples de cet article illustrent des déploiements dans des groupes de ressources.

## <a name="prerequisites"></a>Prérequis

Il vous faut un modèle à déployer. Si vous n’en avez pas déjà un, téléchargez et enregistrez un [exemple de modèle](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) à partir du référentiel de modèles de démarrage rapide Azure. Le nom du fichier local utilisé dans cet article est **c:\MyTemplates\azuredeploy.json**.

À moins d’utiliser Azure Cloud Shell pour déployer les modèles, vous devez installer Azure PowerShell et vous connecter à Azure :

- **Installez les cmdlets Azure PowerShell sur votre ordinateur local.** Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](/powershell/azure/get-started-azureps).
- **Connectez-vous à Azure à l'aide de [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Si vous disposez de plusieurs abonnements Azure, vous devrez peut-être également exécuter [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Pour plus d'informations, consultez [Utiliser plusieurs abonnements Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Déployer un modèle local

L’exemple suivant crée un groupe de ressources et déploie un modèle à partir de votre ordinateur local. Le nom du groupe de ressources ne peut contenir que des caractères alphanumériques, des points, des traits de soulignement, des traits d'union et des parenthèses. Il peut comprendre jusqu’à 90 caractères. Il ne peut pas se terminer par un point.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Le déploiement peut prendre plusieurs minutes.

## <a name="deploy-remote-template"></a>Déployer un modèle distant

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

## <a name="deploy-from-azure-cloud-shell"></a>Déployer à partir d’Azure Cloud Shell

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

## <a name="pass-parameter-values"></a>Passer les valeurs de paramètre

Pour passer les valeurs de paramètre, vous pouvez utiliser des paramètres inline ou un fichier de paramètres.

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

Si vous avez besoin de transmettre un tableau d’objets, créez des tables de hachage dans PowerShell et ajoutez-les à un tableau. Transmettez ce tableau en tant que paramètre lors du déploiement.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Fichiers de paramètres

Au lieu de passer des paramètres en tant que valeurs inline dans votre script, il peut s’avérer plus facile d’utiliser un fichier JSON qui contient les valeurs des paramètres. Le fichier de paramètres peut être un fichier local ou un fichier externe avec un URI accessible.

Pour plus d’informations sur le fichier de paramètres, consultez [Créer un fichier de paramètres Resource Manager](resource-manager-parameter-files.md).

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

- Pour restaurer un déploiement réussi lorsque vous obtenez une erreur, consultez [Restaurer en cas d’erreur vers un déploiement réussi](rollback-on-error.md).
- Pour spécifier comment gérer les ressources présentes dans le groupe de ressources, mais non définies dans le modèle, consultez [Modes de déploiement Azure Resource Manager](deployment-modes.md).
- Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](resource-manager-powershell-sas-token.md).
