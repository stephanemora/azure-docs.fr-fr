---
title: Création d’un modèle Azure Resource Manager
description: Décrit le processus de création d’un modèle Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: abfc7ce78e8676e9560621be1ec9a81717d958e5
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994281"
---
# <a name="create-azure-resource-manager-template"></a>Créer un modèle Azure Resource Manager

Cet article décrit le processus et les décisions prises lors de la création d’un modèle Azure Resource Manager. Il fournit une vue d’ensemble des exemples et des fonctionnalités qui peuvent vous aider lors de la création de votre modèle. Cet article part du principe que vous déployez des ressources sur un groupe de ressources. Si vous devez déployer des ressources dans votre abonnement Azure, notamment dans le cadre d’une création de stratégies Azure ou d’affectations de contrôle d’accès en fonction du rôle, consultez [Créer des groupes de ressources et des ressources pour un abonnement Azure](deploy-to-subscription.md).

## <a name="select-json-editor"></a>Sélection de l’éditeur JSON

Le modèle Resource Manager est un fichier JSON. Pour le modifier, vous avez besoin d’un bon outil de création. Vous avez le choix. Cependant, si vous n’avez pas d’éditeur favori, nous vous conseillons d’installer [Visual Studio Code (VS Code)](https://code.visualstudio.com/). 

Après avoir installé Visual Studio Code, ajoutez l’[extension Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Cette extension ajoute de nombreuses fonctionnalités qui simplifient la création d’un modèle.

![Modèle Visual Studio Code](./media/how-to-create-template/template-visual-studio-code.png)

La capture d’écran montre un modèle Resource Manager ouvert dans Visual Studio Code. 

Pour consulter un didacticiel d’installation de l’extension des outils Resource Manager et savoir comment utiliser VS Code, consultez le [Guide de démarrage rapide : Créer des modèles Azure Resource Manager à l’aide de Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md).

## <a name="understand-the-template-structure"></a>Compréhension de la structure du modèle

Passons en revue les parties du modèle pour comprendre son fonctionnement. Vous n’êtes pas obligé de doter votre modèle de toutes les sections possibles. Voici les sections à retenir :

* La section [parameters](resource-manager-templates-parameters.md), qui affiche les valeurs que vous pouvez spécifier au cours du déploiement pour personnaliser l’infrastructure déployée. 

* La section [variables](resource-manager-templates-variables.md), qui affiche les valeurs utilisées dans le modèle.

* La section [functions](resource-group-authoring-templates.md#functions), qui affiche les expressions de modèle personnalisées utilisées dans votre modèle.

* La section [ressources](resource-manager-templates-resources.md), qui indique les ressources Azure déployées dans votre abonnement.

* La section [outputs](resource-manager-templates-outputs.md), qui indique les valeurs retournées une fois le déploiement terminé.

## <a name="look-for-similar-templates"></a>Recherche de modèles similaires

Vous pouvez facilement trouver un modèle existant qui déploie une solution similaire à ce dont vous avez besoin. Le site [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/) contient des centaines de modèles fournis par des contributeurs de la communauté.

![Référentiel de modèles de démarrage rapide](./media/how-to-create-template/template-quickstart-repo.png)

Parcourez ce référentiel pour trouver un modèle similaire à ce dont vous avez besoin. Ce n’est pas grave si le modèle ne fait pas exactement ce dont vous avez besoin : vous pouvez toujours le personnaliser.

Après avoir trouvé un modèle, sélectionnez **Rechercher sur Github**, puis copiez le fichier **azuredeploy.json** à partir du référentiel. Dans VS Code, créez un fichier nommé **azuredeploy.json** et ajoutez le contenu du fichier de modèle que vous avez copié à partir du référentiel de démarrage rapide.

## <a name="add-resources"></a>Ajout de ressources

Vous souhaiterez probablement personnaliser le modèle téléchargé pour qu’il réponde à vos besoins. Commencez par examiner les ressources déployées. Vous devrez peut-être ajouter, supprimer ou modifier les ressources dans le modèle. Pour connaître les descriptions et syntaxe des ressources, consultez les [références de modèles Azure Resource Manager](/azure/templates/) (page en anglais).

![Référence de modèle](./media/how-to-create-template/template-reference.png)

Après avoir examiné les propriétés, apportez les modifications requises. Pour obtenir des suggestions sur la définition des ressources, consultez les [pratiques recommandées pour les ressources](template-best-practices.md#resources) (page en anglais).

## <a name="add-or-remove-parameters"></a>Ajout ou suppression de paramètres

Vous devrez peut-être également ajuster les paramètres pour votre modèle. Vous pouvez ajouter ou supprimer des paramètres selon le degré de personnalisation de votre choix pendant le déploiement. Pour obtenir des suggestions sur l’utilisation des paramètres, consultez les [pratiques recommandées sur les paramètres](template-best-practices.md#parameters) (page en anglais).

## <a name="add-tags"></a>Ajouter des étiquettes

Vous pouvez ajouter des balises à vos ressources pour les organiser de façon logique par catégories et diviser les coûts de facturation. L’ajout de balises est simple, il vous suffit de les appliquer dans le fichier JSON de la ressource. Par exemple, le compte de stockage suivant a deux balises :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

Vous pouvez également appliquer des balises dynamiquement à partir des paramètres. Pour plus d’informations, dans la documentation Azure Resource Manager, consultez la section [Concepts > Modèles > Ressources > Balises](resource-manager-templates-resources.md#tags).

## <a name="review-template-functions"></a>Examen des fonctions du modèle

Comme vous pouvez le remarquer, votre modèle contient des expressions entourées de crochets. Par exemple : `"[some-expression]"`. Ces expressions utilisent des fonctions de modèle pour construire dynamiquement des valeurs au cours du déploiement.

Par exemple, vous verrez souvent une expression telle que :

```json
"name": "[parameters('siteName')]"
```

Cette expression obtient la valeur d’un paramètre. La valeur est assignée à la propriété name.

Vous pourrez également voir une expression plus complexe qui utilise plusieurs fonctions, telle que :

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Cette expression obtient un objet avec les propriétés d’un compte de stockage.

Pour comprendre ce que font les fonctions, dans la documentation Azure Resource Manager, consultez la section [Informations de référence > Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).

## <a name="create-more-than-one-instance"></a>Création de plusieurs instances

Il peut arriver que souhaitiez créer plusieurs instances d’une ressource. Cela peut être le cas si vous avez besoin de plusieurs comptes de stockage. Plutôt que de répéter la ressource dans votre modèle, vous pouvez utiliser la syntaxe `copy` pour spécifier plusieurs instances.

L’exemple suivant crée trois comptes de stockage :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Vous pouvez également spécifier le nombre d’instances dynamiquement en utilisant un paramètre. Pour plus d’informations, consultez [Déployer plusieurs instances d’une ressource ou d’une propriété dans des modèles Azure Resource Manager](resource-group-create-multiple.md).

## <a name="conditionally-deploy-a-resource"></a>Déploiement d’une ressource de manière conditionnelle

Parfois, vous devez spécifier au cours du déploiement si une ressource du modèle est déployée. Par exemple, vous pouvez souhaiter avoir le choix entre le déploiement d’une nouvelle ressource ou l’utilisation d’une ressource existante. L’élément `condition` vous permet d’activer ou de désactiver le déploiement d’une ressource. Lorsque l’expression dans l’élément de la condition a la valeur True, la ressource est déployée. Si la valeur est False, la ressource est ignorée pendant le déploiement.

L’exemple suivant déploie un compte de stockage de manière conditionnelle :

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Pour plus d’informations, dans la documentation Azure Resource Manager, consultez la section [Concepts > Modèles > Ressources > Condition](resource-manager-templates-resources.md#condition).

## <a name="review-dependencies"></a>Examen des dépendances

Certaines ressources de votre modèle doivent être déployées avant les autres ressources. Par exemple, un serveur SQL doit exister avant la création d’une base de données SQL. Resource Manager détermine implicitement l’ordre de déploiement des ressources lorsque la [fonction de référence](resource-group-template-functions-resource.md#reference) est utilisée. Toutefois, dans certains cas, vous devez définir explicitement les dépendances à l’aide de l’élément `dependsOn`. Examinez votre modèle pour vérifier si l’ajout de toutes les dépendances est nécessaire. Veillez à ne pas ajouter de dépendances inutiles, car elles peuvent ralentir le déploiement ou créer des références circulaires.

L’image suivante montre l’ordre de dépendance de différentes ressources App Service :

![Dépendances des applications web](./media/how-to-create-template/web-dependencies.png)

L’exemple suivant montre une partie d’un modèle qui définit les dépendances.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Pour plus d’informations, consultez [Définir l’ordre de déploiement des ressources dans les modèles Azure Resource Manager](resource-group-define-dependencies.md).

## <a name="review-recommended-practices"></a>Consultation des meilleures pratiques

Avant de déployer votre modèle, passez en revue les [Meilleures pratiques relatives aux modèles Azure Resource Manager](template-best-practices.md) pour vérifier si certaines s’appliquent à votre modèle.

Si vous avez besoin d’utiliser votre modèle dans d’autres environnements de cloud Azure, consultez [Développer des modèles Azure Resource Manager de cohérence du cloud](templates-cloud-consistency.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour déployer un modèle, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](resource-group-template-deploy-cli.md) ou [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](resource-group-template-deploy.md).
* Notre guide de démarrage rapide pas à pas sur la création d’un modèle est disponible sur la page [Démarrage rapide : Créer des modèles Azure Resource Manager à l’aide de Visual Studio Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md).
* Pour obtenir la liste des fonctions disponibles dans un modèle, consultez [Fonctions de modèle](resource-group-template-functions.md).
