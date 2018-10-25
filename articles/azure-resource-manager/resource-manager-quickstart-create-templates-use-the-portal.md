---
title: Créer et déployer un modèle Azure Resource Manager à l’aide du portail Azure | Microsoft Docs
description: Découvrez comment créer votre premier modèle Azure Resource Manager à l’aide du portail Azure et comment le déployer.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/17/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 62bc29495bab3446b6131223110c694e53412db9
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407637"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure

Découvrez comment créer votre premier modèle Azure Resource Manager à l’aide du portail Azure et le processus de modification et de déploiement du modèle à partir du portail Azure. Les modèles Azure Resource Manager sont des fichiers JSON qui définissent les ressources nécessaires au déploiement de votre solution. Suivez les instructions de ce tutoriel pour créer un compte de stockage Azure. Vous pouvez utiliser le même processus pour créer d’autres ressources Azure.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="generate-a-template-using-the-portal"></a>Générer un modèle à l’aide du portail Azure

Dans cette section, vous créez un compte de stockage à l’aide du portail Azure. Avant de déployer le compte de stockage, vous avez la possibilité d’explorer le modèle généré par le portail en fonction de vos configurations. Vous pouvez enregistrer le modèle et le réutiliser ultérieurement.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Créer une ressource** > **Stockage** > **Compte de stockage - blob, fichier, table, file d’attente**.

    ![Créer un compte de stockage Azure dans le portail Azure](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Entrez les informations ci-après. 

    - **Groupe de ressources** : créez un nouveau groupe de ressources Azure portant le nom de votre choix. Sur la capture d’écran, le nom du groupe de ressources est *mystorage1016rg*.
    - **Nom** : attribuez un nom unique à votre compte de stockage. Sur la capture d’écran, le nom est *mystorage1016*.

    Vous pouvez utiliser les valeurs par défaut pour les autres propriétés.

    ![Créer une configuration de compte de stockage Azure à l’aide du portail Azure](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Certains modèles exportés nécessitent des modifications avant leur déploiement.

4. Sélectionnez **Vérifier + créer** au bas de l’écran. 
5. Sélectionnez **Télécharger un modèle pour l’automatisation** au bas de l’écran. Le portail affiche le modèle généré :

    ![Générer un modèle depuis le portail](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    Le volet principal affiche le modèle. Il s’agit d’un fichier JSON avec quatre éléments de niveau supérieur, `schema`, `contentVersion`, `parameters` et `resources`. Pour plus d’informations, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md)

    Il existe six paramètres définis. Un d’eux est appelé **storageAccountName**. La deuxième partie en surbrillance montre comment utiliser ce paramètre dans le modèle. Dans la section suivante, vous modifiez le modèle pour utiliser un nom généré pour le compte de stockage.

    Dans le modèle, une ressource Azure est définie. Le type est [Microsoft.Storage/storageAccounts]. Examinez la façon dont la ressource est définie, et la structure de la définition.
6. Sélectionnez **Télécharger**. Enregistrez le fichier **template.json** à partir du package téléchargé sur votre ordinateur. Dans la section suivante, vous utilisez un outil de déploiement de modèle pour modifier le modèle.
7. Sélectionnez l’onglet **Paramètre** pour afficher les valeurs que vous avez fournies pour les paramètres. Notez ces valeurs, vous en aurez besoin dans la section suivante lors du déploiement du modèle.

    ![Générer un modèle depuis le portail](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    À l’aide du modèle et des fichiers de paramètres, vous pouvez créer un compte de stockage Azure.

## <a name="edit-and-deploy-the-template"></a>Modifier et déployer le modèle

Le portail Azure peut être utilisé pour effectuer des modifications de base du modèle. Dans ce démarrage rapide, vous utilisez un outil du portail appelé *déploiement de modèle*. Pour modifier un modèle plus complexe, envisagez d’utiliser [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md), qui offre plus de fonctionnalités d’édition.

Azure requiert que chaque service Azure ait un nom unique. Le déploiement échoue si vous entrez un nom de compte de stockage déjà existant. Pour éviter ce problème, vous pouvez utiliser un appel de fonction modèle `uniquestring()` pour générer un nom de compte de stockage unique.

1. Dans le portail Azure, sélectionnez **Créer une ressource**.
2. Dans **Rechercher sur la Place de marché**, tapez **déploiement de modèle**, puis appuyez sur **Entrée**.
3. Sélectionnez **Déploiement de modèle**.

    ![Bibliothèque des modèles Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. Sélectionnez **Créer**.
5. Sélectionnez **Générer votre propre modèle dans l’éditeur**.
6. Sélectionnez **Charger le fichier**, puis suivez les instructions pour charger le fichier template.json téléchargé dans la section précédente.
7. Ajoutez une variable comme illustré dans la capture d’écran suivante :

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
    ```
    ![Modèles Microsoft Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

    Deux fonctions sont utilisées ici : `concat()` et `uniqueString()`.

8. Supprimez le paramètre **storageAccountName** mis en surbrillance dans la capture d’écran précédente.
9. Mettez à jour l’élément nom de la ressource **Microsoft.Storage/storageaccounts** pour utiliser la variable qui vient d’être définie au lieu du paramètre :

    ```json
    "name": "[variables('storageAccountName')]",
    ```

    Le modèle final doit ressembler à ce qui suit :

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string"
            },
            "accountType": {
                "type": "string"
            },
            "kind": {
                "type": "string"
            },
            "accessTier": {
                "type": "string"
            },
            "supportsHttpsTrafficOnly": {
                "type": "bool"
            }
        },
        "variables": {
            "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
            {
                "name": "[variables('storageAccountName')]",
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2018-07-01",
                "location": "[parameters('location')]",
                "properties": {
                    "accessTier": "[parameters('accessTier')]",
                    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
                },
                "dependsOn": [],
                "sku": {
                    "name": "[parameters('accountType')]"
                },
                "kind": "[parameters('kind')]"
            }
        ],
        "outputs": {}
    }
    ```
7. Sélectionnez **Enregistrer**.
8. Saisissez les valeurs suivantes :

    - **Groupe de ressources** : attribuez un nom unique à votre groupe de ressources.
    - **Emplacement** : sélectionnez un emplacement pour le groupe de ressources.
    - **Emplacement** : sélectionnez un emplacement pour le compte de stockage.  Vous pouvez utiliser le même emplacement que celui de votre groupe de ressources.
    - **Type de compte** : entrez **Standard_LRS** pour ce démarrage rapide.
    - **Type** : entrez **StorageV2** pour ce démarrage rapide.
    - **Niveau d’accès** : entrez **chaud** pour ce démarrage rapide.
    - **Seul le trafic HTTPS est activé**.  Pour ce guide de démarrage rapide, sélectionnez **true**.
    - **J’accepte les termes et conditions mentionnés ci-dessus** : (cochez la case)

    Voici une capture d’écran d’un exemple de déploiement :

    ![Déploiement de modèles Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Sélectionnez **Achat**.
11. Sélectionnez l’icône représentant une cloche (notifications) en haut de l’écran pour afficher l’état du déploiement. Attendez la fin du déploiement.

    ![Notification de déploiement de modèles Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. Sélectionnez **Accédez au groupe de ressources** à partir du volet de notification. Vous devriez voir un écran semblable à :

    ![Groupe de ressources de déploiement de modèles Azure Resource Manager](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    L’état du déploiement vous montre qu’il a réussi, et il n’y a qu’un seul compte de stockage dans le groupe de ressources. Le nom de compte de stockage est une chaîne unique générée par le modèle. Pour en savoir plus sur l’utilisation des comptes de stockage Azure, consultez [Démarrage rapide : charger, télécharger et répertorier des objets blob à l’aide du portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir le compte de stockage dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a appris à générer un modèle et à le déployer à partir du portail Azure. Le modèle utilisé dans ce démarrage rapide est un modèle simple disposant d’une seule ressource Azure. Lorsque le modèle est complexe, il est plus facile d’utiliser Visual Studio Code ou Visual Studio pour développer le modèle. Le guide de démarrage rapide suivant vous montre également comment déployer des modèles à l’aide d’Azure PowerShell et l’interface de ligne de commande (CLI) Azure.

> [!div class="nextstepaction"]
> [Créer des modèles à l’aide de Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
