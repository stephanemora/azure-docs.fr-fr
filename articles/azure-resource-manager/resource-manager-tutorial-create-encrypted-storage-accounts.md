---
title: Créer un modèle Azure Resource Manager pour le déploiement d’un compte de stockage chiffré | Microsoft Docs
description: Utilisez Visual Studio Code pour créer un modèle pour le déploiement d’un compte de stockage chiffré.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d48374d7919be3d141ea199e8238a220dbfe0332
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419523"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Didacticiel : créer un modèle Azure Resource Manager pour le déploiement d’un compte de stockage chiffré

Découvrez comment trouver des informations pour terminer un modèle Azure Resource Manager.

Dans ce didacticiel, vous utiliserez un modèle de base parmi les modèles Azure Quickstart pour créer un compte de stockage Azure.  À l’aide des documents de référence de modèle, vous personnalisez le modèle de base pour créer un compte de stockage chiffré.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Comprendre le modèle
> * Modifier le modèle
> * Déployer le modèle

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/).
* Extension Outils Azure Resource Manager. Pour l’installation, consultez [Installer l’extension Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Le modèle utilisé dans ce démarrage rapide se nomme [Créer un compte de stockage standard](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Le modèle définit une ressource de compte de stockage Azure.

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer le fichier sous le nom **azuredeploy.json** sur votre ordinateur local.

## <a name="understand-the-format"></a>Comprendre le format

Dans VS Code, réduisez le modèle jusqu’au niveau de la racine. Vous avez la structure la plus simple avec les éléments suivants :

![Structure la plus simple du modèle Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema** : spécifiez l’emplacement du fichier de schéma JSON qui décrit la version du langage du modèle.
* **contentVersion** : spécifiez n’importe quelle valeur pour que cet élément documente les modifications importantes dans votre modèle.
* **parameters** : spécifiez les valeurs fournies lors de l'exécution du déploiement pour personnaliser le déploiement des ressources.
* **variables** : spécifiez les valeurs utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions du langage du modèle.
* **ressources** : spécifiez les types de ressource déployés ou mis à jour dans un groupe de ressources.
* **outputs** : spécifiez les valeurs retournées après le déploiement.

## <a name="use-parameters-in-template"></a>Utiliser des paramètres dans le modèle

Les paramètres vous permettent de personnaliser le déploiement grâce à des valeurs adaptées à un environnement particulier. Vous utilisez les paramètres définis dans le modèle lorsque vous définissez des valeurs pour le compte de stockage.

![Paramètres du modèle Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

Dans ce modèle, deux paramètres sont définis. Remarquez qu'une fonction de modèle est utilisée dans location.defaultValue :

```json
"defaultValue": "[resourceGroup().location]",
```

La fonction resourceGroup() renvoie un objet qui représente le groupe de ressources actuel. Pour obtenir la liste complète des fonctions de modèle, consultez [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md).

Pour utiliser les paramètres définis dans le modèle :

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables-in-template"></a>Utiliser des variables dans le modèle

Les variables vous permettent de construire des valeurs pouvant être utilisées dans votre modèle. Les variables vous aident à réduire la complexité des modèles.

![Variables du modèle Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

Ce modèle définit une variable *storageAccountName*. Dans la définition, deux fonctions de modèle sont utilisées :

- **concat()**  : concatène les chaînes. Pour plus d'informations, consultez [concat](./resource-group-template-functions-string.md#concat).
- **uniqueString()**  : crée une chaîne de hachage déterministe basée sur les valeurs fournies en tant que paramètres. Chaque compte de stockage Azure doit avoir un nom unique sur l’ensemble d’Azure. Cette fonction fournit une chaîne unique. Pour davantage de fonctions de chaîne, consultez [Fonctions de chaîne](./resource-group-template-functions-string.md).

Pour utiliser la variable définie dans le modèle :

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>Modifier le modèle

L’objectif de ce didacticiel est de définir un modèle pour créer un compte de stockage chiffré.  L’exemple de modèle crée uniquement un compte de stockage de base non chiffré. Pour trouver la configuration relative au chiffrement, vous pouvez utiliser la référence du modèle du compte de stockage Azure.

1. Accédez aux [modèles Azure](https://docs.microsoft.com/azure/templates/).
2. Dans **Filtrer par titre**, entrez **comptes de stockage**.
3. Sélectionnez **Référence/Référence de modèle/Stockage/Comptes de stockage**, comme indiqué dans la capture d’écran suivante :

    ![Resource Manager modèle référence compte de stockage](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    resource-manager-template-resources-reference-storage-accounts
1. Explorez les informations relatives au chiffrement.  
1. À l’intérieur de l’élément Propriétés de la définition de ressource de compte de stockage, ajoutez le code json suivant :

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    Cette partie active la fonction de chiffrement du service de stockage d’objets blob.

À partir de Visual Studio Code, modifiez le modèle afin que l’élément de ressources finales ressemble à :

![Ressources de compte de stockage chiffré du modèle Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Déployer le modèle

Reportez-vous à la section [Déployer le modèle](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) dans le guide de démarrage rapide Visual Studio Code pour connaître la procédure de déploiement.

La capture d’écran suivante montre la commande CLI permettant de répertorier le compte de stockage nouvellement créé, ce qui indique que le chiffrement a été activé pour le stockage d’objets blob.

![Compte de stockage chiffré Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser la référence de modèle pour personnaliser un modèle existant. Pour apprendre à créer plusieurs instances de compte de stockage, voir :

> [!div class="nextstepaction"]
> [Créer plusieurs instances](./resource-manager-tutorial-create-multiple-instances.md)
