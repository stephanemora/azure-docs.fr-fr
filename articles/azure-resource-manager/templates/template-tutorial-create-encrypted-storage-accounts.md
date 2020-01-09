---
title: Utiliser la référence de modèle
description: Utiliser la référence du modèle Azure Resource Manager pour créer un modèle de déploiement d’un compte de stockage chiffré.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 1bdd88d3f7dacdfe25645fda123076caadab5aec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75471514"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>Tutoriel : Utiliser la référence du modèle Azure Resource Manager

Découvrez comment trouver les informations concernant le schéma de modèle et utiliser ces informations pour créer des modèles Azure Resource Manager.

Dans ce didacticiel, vous utiliserez un modèle de base pris dans les modèles de démarrage rapide Azure. À l’aide des documents de référence du modèle, vous personnalisez le modèle de base pour créer un compte de stockage chiffré.

![Référence du modèle Resource Manager déployer compte de stockage chiffré](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Comprendre le modèle
> * Trouver la référence du modèle
> * Modifier le modèle
> * Déployer le modèle

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Visual Studio Code avec l’extension Outils Resource Manager. Consultez [Utiliser Visual Studio Code pour créer des modèles Azure Resource Manager](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

[Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/) est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce démarrage rapide se nomme [Créer un compte de stockage standard](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Le modèle définit une ressource de compte de stockage Azure.

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer le fichier sous le nom **azuredeploy.json** sur votre ordinateur local.

## <a name="understand-the-schema"></a>Comprendre le schéma

1. Dans VS Code, réduisez le modèle jusqu’au niveau de la racine. Vous avez la structure la plus simple avec les éléments suivants :

    ![Structure la plus simple du modèle Resource Manager](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema** : spécifiez l’emplacement du fichier de schéma JSON qui décrit la version du langage du modèle.
    * **contentVersion** : spécifiez n’importe quelle valeur pour que cet élément documente les modifications importantes dans votre modèle.
    * **parameters** : spécifiez les valeurs fournies lors de l'exécution du déploiement pour personnaliser le déploiement des ressources.
    * **variables** : spécifiez les valeurs utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions du langage du modèle.
    * **ressources** : spécifiez les types de ressource déployés ou mis à jour dans un groupe de ressources.
    * **outputs** : spécifiez les valeurs retournées après le déploiement.

2. Développer des **ressources**. Une ressource `Microsoft.Storage/storageAccounts` est définie. Le modèle crée un compte de stockage non chiffré.

    ![Définition du modèle de compte de stockage Resource Manager](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>Trouver la référence du modèle

1. Accédez à la [documentation de référence du modèle Azure](https://docs.microsoft.com/azure/templates/).
2. Dans la zone **Filtrer par titre**, entrez **Comptes de stockage**.
3. Sélectionnez **Référence/Référence de modèle/Stockage/&lt;Version/Comptes de stockage**, comme indiqué dans la capture d’écran suivante :

    ![Resource Manager modèle référence compte de stockage](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    Si vous ne savez pas quelle version choisir, utilisez la version la plus récente.

4. Rechercher les informations de définition relatives au chiffrement.

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    Sur la même page Web, la description qui suit confirme que `encryption`l’objet est utilisé pour créer un compte de stockage chiffré.

    ![Resource Manager modèle référence compte de stockage chiffrage](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    Il existe deux façons de gérer la clé de chiffrement. Vous pouvez utiliser des clés de chiffrement gérées par Microsoft avec Storage Service Encryption ou utiliser vos propres clés de chiffrement. Pour que ce didacticiel reste simple, utilisez l’option `Microsoft.Storage`, afin de ne pas avoir à créer un Azure Key Vault.

    ![Resource Manager modèle référence compte de stockage chiffrage objet](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    Votre objet de chiffrement doit ressembler à :

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>Modifier le modèle

À partir de Visual Studio Code, modifiez le modèle afin que l’élément de ressources ressemble à :

![Ressources de compte de stockage chiffré du modèle Resource Manager](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Déployer le modèle

Reportez-vous à la section [Déployer le modèle](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) dans le guide de démarrage rapide Visual Studio Code pour connaître la procédure de déploiement.

La capture d’écran suivante montre la commande CLI permettant de répertorier le compte de stockage nouvellement créé, ce qui indique que le chiffrement a été activé pour le stockage d’objets blob.

![Compte de stockage chiffré Azure Resource Manager](./media/template-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser la référence de modèle pour personnaliser un modèle existant. Pour apprendre à créer plusieurs instances de compte de stockage, voir :

> [!div class="nextstepaction"]
> [Créer plusieurs instances](./template-tutorial-create-multiple-instances.md)
