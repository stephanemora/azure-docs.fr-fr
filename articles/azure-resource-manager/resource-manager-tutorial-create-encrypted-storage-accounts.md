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
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7c78636a210ae90c5bfe1d0bfd35e4e05633f5cd
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188197"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Didacticiel : créer un modèle Azure Resource Manager pour le déploiement d’un compte de stockage chiffré

Découvrez comment trouver des informations pour terminer un modèle Azure Resource Manager.

Dans ce didacticiel, vous utiliserez un modèle de base parmi les modèles Azure Quickstart pour créer un compte de stockage Azure.  À l’aide des documents de référence de modèle, vous personnalisez le modèle de base pour créer un compte de stockage chiffré.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Comprendre le format du modèle
> * Utiliser des paramètres dans le modèle
> * Utiliser des variables dans le modèle
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

Pour trouver la configuration relative au chiffrement du compte de stockage, vous pouvez utiliser la référence du modèle du compte de stockage Azure.

1. Accédez aux [modèles Azure](https://docs.microsoft.com/azure/templates/).
2. Dans la table des matières sur la gauche, sélectionnez **Référence**->**Stockage**->**Comptes de stockage**. La page contient les informations pour définir des informations de compte de stockage.
3. Explorez les informations relatives au chiffrement.  
4. À l’intérieur de l’élément Propriétés de la définition de ressource de compte de stockage, ajoutez le code json suivant :

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

L’élément de ressources final ressemble à :

![Ressources de compte de stockage chiffré du modèle Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Déployer le modèle

Il existe de nombreuses méthodes pour déployer des modèles.  Dans ce didacticiel, vous utilisez Cloud Shell dans le portail Azure. Cloud Shell prend en charge Azure CLI et Azure PowerShell. Les instructions fournies ici correspondent à l’interface CLI.

1. Connectez-vous au [portail Azure](https://portal.azure.com)
2. Sélectionnez **Cloud Shell** à partir de l’angle supérieur droit, comme indiqué dans l’image suivante :

    ![Cloud shell du portail Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell.png)

3. Sélectionnez la flèche vers le bas, puis sélectionnez **Bash** si ce n’est pas Bash. Vous utilisez Azure CLI dans ce didacticiel.

    ![Interface CLI du Cloud Shell du portail Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-choose-cli.png)
4. Sélectionnez **Redémarrer** pour redémarrer l’interpréteur de commandes.
5. Sélectionnez **Charger/Télécharger des fichiers**, puis **Charger**.

    ![Fichier de chargement du Cloud Shell du portail Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-upload-file.png)
6. Sélectionnez le fichier que vous avez enregistré précédemment dans ce didacticiel. Le nom par défaut est **azuredeploy.json**.
7. À partir de Cloud Shell, exécutez la commande **ls** pour vérifier que le fichier est chargé avec succès. Vous pouvez également utiliser la commande **cat** pour vérifier le contenu du modèle.

    ![Fichier de liste du Cloud Shell du portail Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-list-file.png)
8. Dans le Cloud Shell exécutez les commandes suivantes :

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file azuredeploy.json
    ```
    Voici une capture d’écran d’un exemple de déploiement :

    ![déploiement du modèle du Cloud shell du portail Azure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-deploy-template.png)

    Sur la capture d’écran, les valeurs suivantes sont utilisées :

    * **&lt;ResourceGroupName>**  : myresourcegroup0719. Le paramètre peut apparaître sous deux formes.  Veillez à utiliser la même valeur.
    * **&lt;AzureLocation >** : eastus2
    * **&lt;DeployName>**  : mydeployment0719
    * **&lt;TemplateFile>** : azuredeploy.json

    À partir de la sortie de la capture d’écran, le nom du compte de stockage est *fhqbfslikdqdsstandardsa*. 

9. Exécutez la commande PowerShell suivante pour lister les comptes de stockage nouvellement créés :

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

    Vous devriez voir une sortie similaire à la capture d’écran suivante, ce qui indique que le chiffrement a été activé pour le stockage d’objets blob.

    ![Compte de stockage chiffré Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser la référence de modèle pour personnaliser un modèle existant. Le modèle utilisé dans ce didacticiel ne contient qu’une seule ressource Azure.  Dans le tutoriel suivant, vous développez un modèle disposant de plusieurs ressources.  Certaines ressources comportent des ressources dépendantes.

> [!div class="nextstepaction"]
> [Créer plusieurs ressources](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
