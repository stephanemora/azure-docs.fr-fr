---
title: Automatiser l’ajout d’un utilisateur de laboratoire dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment automatiser l’ajout d’un utilisateur de laboratoire à un laboratoire dans Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 2ad81ae97414abbf3266cc5728febf9abe836151
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522957"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatiser l’ajout d’un utilisateur de laboratoire à un laboratoire dans Azure DevTest Labs
Azure DevTest Labs vous permet de créer rapidement des environnements de développement-test libre-service à l’aide du portail Azure. Toutefois, si vous avez plusieurs équipes et plusieurs instances de DevTest Labs, automatisant le processus de création peut gagner du temps. [Modèles Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) vous permettent de créer des laboratoires, les machines virtuelles de laboratoire, les images personnalisées, les formules, et ajouter des utilisateurs de manière automatique. Cet article se concentre spécifiquement sur l’ajout d’utilisateurs à une instance de dev/test.

Pour ajouter un utilisateur à un laboratoire, vous ajoutez l’utilisateur à la **utilisateur de DevTest Labs** rôle pour le laboratoire. Cet article vous montre comment automatiser l’ajout d’un utilisateur à un laboratoire à l’aide d’une des manières suivantes :

- Modèles Microsoft Azure Resource Manager
- Applets de commande Azure PowerShell 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Utiliser les modèles Azure Resource Manager
L’exemple de modèle Resource Manager suivant spécifie un utilisateur à ajouter à la **utilisateur de DevTest Labs** rôle d’un laboratoire. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Si vous attribuez le rôle dans le même modèle que celui qui est créé le laboratoire, pensez à ajouter une dépendance entre la ressource d’attribution de rôle et le laboratoire. Pour plus d’informations, consultez [définition de dépendances dans les modèles Azure Resource Manager](../azure-resource-manager/resource-group-define-dependencies.md) article.

### <a name="role-assignment-resource-information"></a>Informations de ressource d’attribution de rôle
La ressource d’attribution de rôle doit spécifier le nom et le type.

La première chose à noter est que le type de la ressource n’est pas `Microsoft.Authorization/roleAssignments` comme il le serait pour un groupe de ressources.  Au lieu de cela, le type de ressource suit le modèle `{provider-namespace}/{resource-type}/providers/roleAssignments`. Dans ce cas, le type de ressource sera `Microsoft.DevTestLab/labs/providers/roleAssignments`.

Le nom de l’attribution de rôle lui-même doit être globalement unique.  Le nom de l’assignation utilise le modèle `{labName}/Microsoft.Authorization/{newGuid}`. Le `newGuid` est une valeur de paramètre pour le modèle. Elle garantit que le nom de l’attribution de rôle est unique. Qu’il n’y a aucune fonction de modèle pour la création des GUID, vous devez générer un GUID à l’aide de n’importe quel outil Générateur GUID.  

Dans le modèle, le nom de l’attribution de rôle est défini par le `fullDevTestLabUserRoleName` variable. La ligne exacte à partir du modèle est :

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Propriétés de ressource de l’attribution de rôle
Une attribution de rôle lui-même définit trois propriétés. Il doit le `roleDefinitionId`, `principalId`, et `scope`.

### <a name="role-definition"></a>Définition de rôle
L’ID de définition de rôle est l’identificateur de chaîne pour la définition de rôle existante. Le rôle ID se présente sous la forme `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

L’abonnement ID est obtenu à l’aide de `subscription().subscriptionId` fonction de modèle.  

Vous devez obtenir la définition de rôle pour le `DevTest Labs User` rôle intégré. Pour obtenir le GUID pour le [utilisateur de DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) rôle, vous pouvez utiliser la [API REST d’attributions de rôle](/rest/api/authorization/roleassignments) ou [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) applet de commande.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

L’ID de rôle est défini dans la section des variables et nommé `devTestLabUserRoleId`. Dans le modèle, l’ID de rôle est définie sur : 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID du principal
ID du principal est l’ID d’objet de l’utilisateur Active Directory, un groupe ou un principal de service que vous souhaitez ajouter en tant qu’un utilisateur de laboratoire au laboratoire. Le modèle utilise le `ObjectId` en tant que paramètre.

Vous pouvez obtenir l’ID d’objet à l’aide de la [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup, ou [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) applets de commande PowerShell. Ces applets de commande retournent un seul ou listes d’objets Active Directory qui ont une propriété d’ID, qui est l’ID d’objet dont vous avez besoin. L’exemple suivant montre comment obtenir l’ID d’objet d’un utilisateur unique dans une entreprise.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Vous pouvez également utiliser les applets de commande Azure Active Directory PowerShell qui incluent [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0), et [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>`Scope`
Étendue Spécifie la ressource ou le groupe de ressources pour lequel l’attribution de rôle doit s’appliquer. Pour les ressources, l’étendue est sous la forme : `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. Utilise le modèle le `subscription().subscriptionId` fonction pour renseigner le `subscription-id` partie et la `resourceGroup().name` fonction de modèle pour renseigner le `resource-group-name` partie. À l’aide de ces fonctions signifie que le lab auquel vous attribuez un rôle doit exister dans l’abonnement actuel et le même groupe de ressources sur lequel le déploiement du modèle est effectué. La dernière partie, `resource-name`, est le nom de l’atelier. Cette valeur est reçue par le biais du paramètre de modèle dans cet exemple. 

L’étendue du rôle dans le modèle : 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Déploiement du modèle
Tout d’abord, créez un fichier de paramètres (par exemple : azuredeploy.parameters.json) qui passe les valeurs des paramètres dans le modèle Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Ensuite, utilisez le [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) applet de commande PowerShell pour déployer le modèle Resource Manager. La commande suivante affecte une personne, groupe ou un principal de service au rôle utilisateur de DevTest Labs pour un laboratoire.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Il est important de noter que la groupe déploiement nom et affectation des rôles GUID doivent être uniques. Si vous essayez de déployer une attribution de ressources avec un GUID non unique, vous obtiendrez un `RoleAssignmentUpdateNotPermitted` erreur.

Si vous envisagez d’utiliser le modèle plusieurs fois pour ajouter plusieurs objets Active Directory pour le rôle d’utilisateur de DevTest Labs pour votre laboratoire, envisagez d’utiliser des objets dynamiques dans votre commande PowerShell. L’exemple suivant utilise le [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) applet de commande pour spécifier dynamiquement de la ressource groupe déploiement rôle attribution des noms et GUID.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell
Comme indiqué dans l’introduction, vous créez une nouvelle attribution de rôle Azure pour ajouter un utilisateur à la **utilisateur de DevTest Labs** rôle pour le laboratoire. Dans PowerShell, vous le faire à l’aide de la [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) applet de commande. Cette applet de commande possède de nombreux paramètres facultatifs pour permettre une grande flexibilité. Le `ObjectId`, `SigninName`, ou `ServicePrincipalName` peut être spécifié en tant que l’objet de disposer des autorisations.  

Voici un exemple de commande Azure PowerShell qui ajoute un utilisateur au rôle d’utilisateur de DevTest Labs dans le laboratoire spécifié.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Pour spécifier la ressource à laquelle les autorisations sont en cours spécifiée accordée par une combinaison de `ResourceName`, `ResourceType`, `ResourceGroup` ou par le `scope` paramètre. La combinaison de paramètres est utilisée, fournir suffisamment d’informations pour l’applet de commande pour identifier de manière unique l’objet Active Directory (utilisateur, groupe ou principal de service), étendue (groupe de ressources ou ressource) et définition de rôle.

## <a name="use-azure-command-line-interface-cli"></a>Utiliser l’Interface de ligne de commande (CLI) Azure
Dans Azure CLI, l’ajout d’un utilisateur de laboratoires à un laboratoire est effectuée à l’aide de la `az role assignment create` commande. Pour plus d’informations sur les applets de commande Azure CLI, consultez [gérer l’accès aux ressources Azure à l’aide de RBAC et Azure CLI](../role-based-access-control/role-assignments-cli.md).

L’objet qui doit être accordée à l’accès peut être spécifié par le `objectId`, `signInName`, `spn` paramètres. Le laboratoire dans lequel l’objet doit être accordée à l’accès peut être identifié par le `scope` url ou une combinaison de la `resource-name`, `resource-type`, et `resource-group` paramètres.

L’exemple Azure CLI suivant vous montre comment ajouter une personne au rôle d’utilisateur de DevTest Labs pour le laboratoire spécifié.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [Créer et gérer des machines virtuelles avec DevTest Labs à l’aide de l’interface CLI Azure](devtest-lab-vmcli.md)
- [Créer une machine virtuelle avec DevTest Labs à l’aide d’Azure PowerShell](devtest-lab-vm-powershell.md)
- [Utiliser des outils de ligne de commande pour démarrer et arrêter des machines virtuelles Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

