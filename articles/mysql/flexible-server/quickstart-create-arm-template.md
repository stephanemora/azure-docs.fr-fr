---
title: "Démarrage rapide : Créer une instance d'Azure DB pour MySQL - Serveur flexible - Modèle Resource Manager"
description: Dans ce guide de démarrage rapide, vous allez apprendre à créer une instance d'Azure Database pour MySQL - Serveur flexible à l'aide d'un modèle Resource Manager.
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 10/23/2020
ms.openlocfilehash: 5be0bf21514dd92c2f980081bb57d873895bbb91
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099929"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql---flexible-server-preview"></a>Démarrage rapide : Utiliser un modèle Resource Manager pour créer une instance d'Azure Database pour MySQL - Serveur flexible (préversion)

> [!IMPORTANT]
> Azure Database pour MySQL - Serveur flexible est actuellement en préversion publique.

Azure Database pour MySQL - Serveur flexible (préversion) est un service géré qui vous permet d'exécuter, de gérer et de mettre à l'échelle des bases de données MySQL hautement disponibles dans le cloud. Vous pouvez utiliser des modèles Azure Resource Manager (modèles ARM) pour provisionner un serveur flexible afin de déployer plusieurs serveurs ou bases de données sur un serveur.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prérequis

Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Vérifier le modèle

Un serveur flexible Azure Database pour MySQL est la ressource parente d’une ou plusieurs bases de données au sein d’une région. Il fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexion, pare-feu, utilisateurs, rôles, configurations.

Créez un fichier _mysql-flexible-server-template.json_ et copiez-y ce script JSON.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "administratorLogin": {
      "type": "String"
    },
    "administratorLoginPassword": {
      "type": "SecureString"
    },
    "location": {
      "type": "String"
    },
    "serverName": {
      "type": "String"
    },
    "serverEdition": {
      "type": "String"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "haEnabled": {
      "type": "string",
      "defaultValue": "Disabled"
    },
    "availabilityZone": {
      "type": "String"
    },
    "version": {
      "type": "String"
    },
    "tags": {
      "defaultValue": {},
      "type": "Object"
    },
    "firewallRules": {
      "defaultValue": {},
      "type": "Object"
    },
    "vnetData": {
      "defaultValue": {},
      "type": "Object"
    },
    "backupRetentionDays": {
      "type": "Int"
    }
  },
  "variables": {
    "api": "2020-07-01-preview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_D4ds_v4",
        "tier": "[parameters('serverEdition')]"
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "DelegatedSubnetArguments": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "haEnabled": "[parameters('haEnabled')]",
        "storageProfile": {
          "storageMB": "[parameters('storageSizeMB')]",
          "backupRetentionDays": "[parameters('backupRetentionDays')]"
        },
        "availabilityZone": "[parameters('availabilityZone')]"
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-08-01",
      "name": "[concat('firewallRules-', copyIndex())]",
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforMySQL/flexibleServers/firewallRules",
              "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
              "apiVersion": "[variables('api')]",
              "properties": {
                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
              }
            }
          ]
        }
      },
      "copy": {
        "name": "firewallRulesIterator",
        "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
        "mode": "Serial"
      },
      "condition": "[greater(length(variables('firewallRules')), 0)]"
    }
  ]
}
```

Ces ressources Azure sont définies dans le modèle :

- Microsoft.DBforMySQL/flexibleServers

## <a name="deploy-the-template"></a>Déployer le modèle

Sélectionnez **Essayer** à partir du bloc de code PowerShell suivant pour ouvrir [Azure Cloud Shell](../../cloud-shell/overview.md).

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Procédez comme suit pour vérifier que votre serveur a été créé dans Azure.

### <a name="azure-portal"></a>Portail Azure

1. Dans le [portail Azure](https://portal.azure.com), cherchez et sélectionnez **Serveurs Azure Database pour MySQL**.
1. Dans la liste des bases de données, sélectionnez votre nouveau serveur. La page **Vue d’ensemble** de votre nouveau serveur Azure Database pour MySQL s’affiche.

### <a name="powershell"></a>PowerShell

Vous devrez entrer le nom du nouveau serveur pour voir les détails de votre serveur flexible Azure Database pour MySQL.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>Interface de ligne de commande

Vous devrez entrer le nom et le groupe de ressources du nouveau serveur pour voir les détails de votre serveur flexible Azure Database pour MySQL.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```
## <a name="exporting-arm-template-from-the-portal"></a>Exportation d’un modèle ARM à partir du portail
Vous pouvez [exporter un modèle ARM](../../azure-resource-manager/templates/export-template-portal.md) à partir du portail Azure. Il existe deux façons d’exporter un modèle :

- [Exporter à partir d’un groupe de ressources ou d’une ressource](../../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Cette option génère un nouveau modèle à partir de ressources existantes. Le modèle exporté est un « instantané » de l’état actuel du groupe de ressources. Vous pouvez exporter un groupe de ressources complet ou des ressources spécifiques au sein de ce groupe de ressources.
- [Exporter avant le déploiement ou à partir de l’historique](../../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Cette option récupère une copie exacte d’un modèle utilisé pour le déploiement.

Quand vous exportez le modèle, vous remarquez, dans la section ```"parameters":{ }``` du modèle, que les paramètres ```administratorLogin``` et ```administratorLoginPassword``` ne sont pas inclus pour des raisons de sécurité. Vous **DEVEZ** ajouter ces paramètres à votre modèle avant son déploiement. Sinon, le modèle échouera.

```
"administratorLogin": {
      "type": "String"
    },
"administratorLoginPassword": {
      "type": "SecureString"
    },
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Conservez ce groupe de ressources, ce serveur et cette base de données unique pour accéder aux [étapes suivantes](#next-steps). Les étapes suivantes vous montrent comment vous connecter à votre base de données et comment l’interroger via différentes méthodes.

Pour supprimer le groupe de ressources :

### <a name="azure-portal"></a>Portail Azure

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **Groupes de ressources**.
1. Dans la liste des groupes de ressources, choisissez le nom de votre groupe de ressources.
1. Dans la page **Vue d’ensemble** de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
1. Dans la boîte de dialogue de confirmation, entrez le nom de votre groupe de ressources, puis sélectionnez **Supprimer**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>Interface de ligne de commande

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```
---

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un tutoriel pas à pas expliquant comment créer un modèle ARM, consultez :

> [!div class="nextstepaction"]
> [Créer et déployer votre premier modèle Resource Manager](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Pour accéder à un tutoriel pas à pas sur la création d'une application avec App Service à l'aide de MySQL, consultez :

> [!div class="nextstepaction"]
>[Créer une application web PHP (Laravel) avec MySQL](tutorial-php-database-app.md)
