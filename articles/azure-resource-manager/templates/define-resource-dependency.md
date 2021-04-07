---
title: Définir l’ordre de déploiement des ressources
description: Décrit la procédure permettant de définir une ressource Azure comme dépendante d’une autre ressource au cours du déploiement. Les dépendances garantissent que le déploiement des ressources s’effectue dans l’ordre adéquat.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934745"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Définir l’ordre de déploiement des ressources dans les modèles ARM

Quand vous déployez des ressources, vous devez éventuellement vous assurer que certaines ressources existent au préalable avant d’autres ressources. Par exemple, vous avez besoin d’un serveur SQL logique avant de déployer une base de données. Vous établissez cette relation en marquant une seule ressource comme dépendante de l’autre ressource. Utilisez l’élément `dependsOn` pour définir une dépendance explicite. Utilisez les fonctions **reference** ou **list** pour définir une dépendance implicite.

Azure Resource Manager évalue les dépendances entre les ressources et les déploie dans leur ordre dépendant. Quand les ressources ne dépendent pas les unes des autres, Resource Manager les déploie en parallèle. Vous devez uniquement définir des dépendances pour les ressources qui sont déployées dans le même modèle.

## <a name="dependson"></a>dependsOn

Dans votre modèle Azure Resource Manager (modèle ARM), l’élément `dependsOn` vous permet de définir une ressource comme dépendante d’une ou de plusieurs ressources. Sa valeur est un tableau JavaScript Object Notation (JSON) de chaînes, chacune d’elles représentant un nom ou un ID de ressource. Le tableau peut inclure des ressources [déployées de manière conditionnelle](conditional-resource-deployment.md). Quand une ressource conditionnelle n’est pas déployée, Azure Resource Manager la supprime automatiquement des dépendances nécessaires.

L’exemple suivant montre une interface réseau qui dépend d’un réseau virtuel, d’un groupe de sécurité réseau et d’une adresse IP publique. Pour obtenir le modèle complet, consultez le [modèle de démarrage rapide pour une machine virtuelle Linux](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json).

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

Vous pouvez être tenté d’utiliser `dependsOn` pour mapper les relations entre vos ressources. Il est toutefois important de comprendre pourquoi vous le faites. Par exemple, pour documenter la manière dont les ressources sont liées entre elles, `dependsOn` n’est pas la bonne approche. Vous ne pouvez pas lancer de requête pour savoir quelles ressources ont été définies dans l’élément `dependsOn` après le déploiement. La définition de dépendances inutiles ralentit le temps de déploiement, Resource Manager ne pouvant pas déployer ces ressources en parallèle.

## <a name="child-resources"></a>Ressources enfants

Aucune dépendance de déploiement implicite n’est créée automatiquement entre une [ressource enfant](child-resource-name-type.md) et la ressource parent. Si vous devez déployer la ressource enfant après la ressource parent, définissez la propriété `dependsOn`.

L’exemple suivant montre un serveur SQL et une base de données. Notez qu’une dépendance explicite est définie entre la base de données et le serveur, même si la base de données est un enfant du serveur.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

Pour obtenir le modèle complet, consultez [Modèle de démarrage rapide pour Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>fonctions reference et list

La [fonction de référence](template-functions-resource.md#reference) permet à une expression de tirer sa valeur d’un autre nom JSON et de paires de valeurs ou de ressources runtime. Les [fonctions list*](template-functions-resource.md#list) renvoient les valeurs d’une ressource à partir d’une opération de liste.

Les expressions de référence et de liste déclarent implicitement qu’une ressource dépend d’une autre. Si possible, utilisez une référence implicite pour éviter d’ajouter une dépendance inutile.

Pour appliquer une dépendance implicite, reportez-vous à la ressource par son nom, et non par son ID de ressource. Si vous transmettez l’ID de ressource dans les fonctions reference ou list, une référence implicite n’est pas créée.

Le format général de la fonction `reference` est :

```json
reference('resourceName').propertyPath
```

Le format général de la fonction `listKeys` est :

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

Dans l’exemple suivant, un point de terminaison CDN dépend explicitement du profil CDN et implicitement d’une application web.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Pour plus d’informations, consultez la [fonction de référence](template-functions-resource.md#reference).

## <a name="depend-on-resources-in-a-loop"></a>En fonction des ressources dans une boucle

Pour déployer des ressources qui dépendent des ressources d’une [boucle de copie](copy-resources.md), deux options s’offrent à vous. Vous pouvez définir une dépendance sur des ressources individuelles dans la boucle ou sur l’ensemble de la boucle.

> [!NOTE]
> Dans la plupart des cas, vous devez définir la dépendance sur des ressources individuelles au sein de la boucle de copie. N’utilisez l’ensemble de la boucle lorsque vous avez besoin que toutes les ressources de la boucle existent avant de créer la ressource suivante. La définition de la dépendance sur l’ensemble de la boucle entraîne une expansion importante du graphique des dépendances, surtout si ces ressources en boucle dépendent d’autres ressources. Cette expansion des dépendances entrave l’efficacité du déploiement.

L’exemple suivant montre comment déployer plusieurs machines virtuelles. Le modèle crée le même nombre d’interfaces réseau. Chaque ordinateur virtuel dépend d’une seule interface réseau, et non de l’ensemble de la boucle.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

L’exemple suivant montre comment déployer trois comptes de stockage avant de déployer la machine virtuelle. Notez que le nom de l’élément `copy` a `name` défini sur `storagecopy` et que l’élément `dependsOn` de la machine virtuelle est également défini sur `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>Dépendances circulaires

Resource Manager identifie les dépendances circulaires lors de la validation du modèle. Si vous recevez une erreur indiquant qu’il existe une dépendance circulaire, évaluez votre modèle pour voir si certaines dépendances peuvent être supprimées. Si la suppression de ces dépendances n’a aucun effet, vous pouvez éliminer les dépendances circulaires en déplaçant certaines opérations de déploiement dans les ressources enfants. Déployez les ressources enfants après les ressources présentant la dépendance circulaire. Par exemple, supposons que vous déployiez deux machines virtuelles, mais que vous deviez définir sur chacune d’elles des propriétés faisant référence les unes aux autres. Vous pouvez les déployer dans l’ordre suivant :

1. Machine virtuelle 1
2. Machine virtuelle 2
3. L’extension sur la machine virtuelle 1 dépend des machines virtuelles 1 et 2. L’extension définit sur la machine virtuelle 1 des valeurs qu’elle obtient de la machine virtuelle 2.
4. L’extension sur la machine virtuelle 2 dépend des machines virtuelles 1 et 2. L’extension définit sur la machine virtuelle 2 des valeurs qu’elle obtient de la machine virtuelle 1.

Pour plus d’informations sur l’évaluation de l’ordre de déploiement et la résolution des erreurs de dépendance, consultez l’article [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour suivre un tutoriel, consultez [Tutoriel : Créer des modèles ARM avec des ressources dépendantes](template-tutorial-create-templates-with-dependent-resources.md).
* Pour lire un module Microsoft Learn qui aborde les dépendances de ressources, consultez [Gérer des déploiements cloud complexes à l’aide des fonctionnalités avancées de modèle ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Pour obtenir des recommandations lors de la définition des dépendances, consultez [Meilleures pratiques relatives aux modèles ARM](template-best-practices.md).
* Pour en savoir plus sur la résolution des problèmes liés aux dépendances lors du déploiement, consultez [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](common-deployment-errors.md).
* Pour apprendre à créer des modèles Azure Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles ARM](template-syntax.md).
* Pour obtenir la liste des fonctions disponibles dans un modèle, consultez [Fonctions de modèle ARM](template-functions.md).
