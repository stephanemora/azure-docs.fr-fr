---
title: Créer des modèles Azure Resource Manager liés | Microsoft Docs
description: Découvrez comment créer des modèles Azure Resource Manager liés pour la création de machine virtuelle
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b08013941c1cf83b3eb006543d699eb7e1356ff0
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239982"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Tutoriel : créer des modèles Azure Resource Manager liés

Découvrez comment créer des modèles Azure Resource Manager liés. Avec des modèles liés, un modèle peut appeler un autre modèle. C’est idéal pour la modularisation de modèles. Dans ce tutoriel, vous utilisez le même modèle que celui utilisé dans [Tutoriel : créer plusieurs instances de ressources à l’aide de modèles Resource Manager](./resource-manager-tutorial-create-multiple-instances.md), qui crée une machine virtuelle, un réseau virtuel et d’autres ressources dépendantes, notamment un compte de stockage. Vous séparez la ressource de compte de stockage à un modèle lié.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Créer le modèle lié
> * Charger le modèle lié
> * Lien vers le modèle lié
> * Configurer la dépendance
> * Déployer le modèle

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/) avec l’[extension Outils Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de la machine virtuelle. Voici un exemple pour générer un mot de passe :

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault a été conçu pour protéger les clés et autres secrets de chiffrement. Pour plus d’informations, consultez [Didacticiel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./resource-manager-tutorial-use-key-vault.md). Nous vous recommandons également de mettre à jour votre mot de passe tous les trois mois.

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Modèles de démarrage rapide Azure est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce didacticiel se nomme [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). Il s’agit du même modèle que celui utilisé dans [Tutoriel : créer plusieurs instances de ressources à l’aide de modèles Resource Manager](./resource-manager-tutorial-create-multiple-instances.md). Vous enregistrez deux copies du même modèle à utiliser en tant que :

* **Modèle principal** : créez toutes les ressources à l’exception du compte de stockage.
* **Modèle lié** : créez le compte de stockage.

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Cinq ressources sont définies par le modèle :

    * `Microsoft.Storage/storageAccounts`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). 
    * `Microsoft.Network/publicIPAddresses`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses). 
    * `Microsoft.Network/virtualNetworks`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks). 
    * `Microsoft.Network/networkInterfaces`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces). 
    * `Microsoft.Compute/virtualMachines`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Il est préférable de comprendre quelques notions basiques du modèle avant de le personnaliser.
5. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.
6. Sélectionnez **Fichier**>**Enregistrer sous** pour créer une autre copie du fichier avec le nom **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Créer le modèle lié

Le modèle lié crée un compte de stockage. Le modèle lié est presque identique au modèle autonome qui crée un compte de stockage. Dans ce tutoriel, le modèle lié doit passer une valeur au modèle principal. Cette valeur est définie dans l’élément `outputs`.

1. Ouvrez linkedTemplate.json dans Visual Studio Code s’il n’est pas ouvert.
2. Effectuez les modifications suivantes :

    * Supprimez toutes les ressources à l’exception du compte de stockage. Vous supprimez quatre ressources au total.
    * Mettez à jour l’élément **outputs**, de sorte qu’il ressemble à ce qui suit :

        ```json
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
        ```
        **storageUri** est requis par la définition de ressource de machine virtuelle dans le modèle principal.  Vous passez la valeur au modèle principal en tant que valeur de sortie.
    * Supprimez les paramètres qui ne sont jamais utilisés. Ces paramètres sont soulignés par une ligne verte ondulée. Il doit rester un seul paramètre appelé **location**.
    * Supprimez l’élément **variables**. Ils ne sont pas nécessaires dans ce tutoriel.
    * Ajoutez un paramètre appelé **storageAccountName**. Le nom du compte de stockage est passé du modèle principal au modèle lié en tant que paramètre.

    Lorsque vous avez terminé, le modèle doit ressembler à ce qui suit :

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
            "type": "string",
            "metadata": {
              "description": "Azure Storage account name."
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
          }
        ],
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
    }
    ```
3. Enregistrez les modifications.

## <a name="upload-the-linked-template"></a>Charger le modèle lié

Les modèles doivent être accessibles à partir de l’emplacement où vous exécutez le déploiement. Cet emplacement peut être un compte de stockage Azure, Github ou Dropbox. Si vos modèles contiennent des informations sensibles, assurez-vous d’en protéger l’accès. Dans ce tutoriel, vous utilisez la méthode de déploiement Cloud Shell comme dans [Tutoriel : créer plusieurs instances de ressources à l’aide de modèles Resource Manager](./resource-manager-tutorial-create-multiple-instances.md). Le modèle principal (azuredeploy.json) est chargé dans l’interpréteur de commandes (shell). Le modèle lié (linkedTemplate.json) doit être partagé quelque part.  Pour limiter les tâches de ce tutoriel, le modèle lié défini dans la section précédente a été chargé vers [un compte de stockage Azure](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json).

## <a name="call-the-linked-template"></a>Appeler le modèle lié

Le modèle principal se nomme azuredeploy.json.

1. Ouvrez azuredeploy.json dans Visual Studio Code s’il n’est pas ouvert.
2. Supprimez la définition de ressource du compte de stockage dans le modèle.
3. Ajoutez l’extrait de code json suivant à l’endroit où se trouvait la définition du compte de stockage :

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Prenez note des détails suivants :

    * Une ressource `Microsoft.Resources/deployments` dans le modèle principal est utilisée pour créer le lien avec un autre modèle.
    * La ressource `deployments` se nomme `linkedTemplate`. Ce nom est utilisé pour [configurer la dépendance](#configure-dependency).  
    * Vous pouvez uniquement utiliser le mode de déploiement [Incremental](./deployment-modes.md) lors de l’appel de modèles liés.
    * `templateLink/uri` contient l’URI du modèle lié. Le modèle lié a été chargé vers un compte de stockage partagé. Si vous chargez le modèle dans un autre emplacement sur Internet, vous pouvez mettre à jour l’URI.
    * Pour passer des valeurs du modèle principal au modèle lié, utilisez `parameters`.
4. Enregistrez les modifications.

## <a name="configure-dependency"></a>Configurer la dépendance

N’oubliez pas, selon [Tutoriel : créer plusieurs instances de ressources à l’aide du modèle Resource Manager](./resource-manager-tutorial-create-multiple-instances.md), la ressource de machine virtuelle dépend du compte de stockage :

![Diagramme de dépendance des modèles Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Étant donné que le compte de stockage est maintenant défini dans le modèle lié, vous devez mettre à jour les deux éléments suivants de la ressource `Microsoft.Compute/virtualMachines`.

* Reconfigurez l’élément `dependOn`. La définition du compte de stockage est déplacée vers le modèle lié.
* Reconfigurez l’élément `properties/diagnosticsProfile/bootDiagnostics/storageUri`. Dans [Créer le modèle lié](#create-the-linked-template), vous avez ajouté une valeur de sortie :

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Cette valeur est requise par le modèle principal.

1. Ouvrez azuredeploy.json dans Visual Studio Code s’il n’est pas ouvert.
2. Développez la définition de ressource de machine virtuelle, mettez à jour **dependsOn** comme indiqué dans la capture d’écran suivante :

    ![Dépendance configurée par les modèles liés Azure Resource Manager ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    Le nom de la ressource de déploiement *linkedTemplate*.  
3. mettez à jour **properties/diagnosticsProfile/bootDiagnostics/storageUri** comme indiqué dans la capture d’écran précédente.

Pour plus d’informations, consultez [Utiliser des modèles liés et imbriqués lors du déploiement de ressources Azure](./resource-group-linked-templates.md)

## <a name="deploy-the-template"></a>Déployer le modèle

Reportez-vous à la section [Déployer le modèle](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) pour connaître la procédure de déploiement. Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de la machine virtuelle. Consultez les [Conditions préalables](#prerequisites).

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez développé et déployé un modèle lié. Pour découvrir comment utiliser les extensions de machine virtuelle afin d’exécuter les tâches post-déploiement, consultez

> [!div class="nextstepaction"]
> [Déployer des extensions de machine virtuelle](./deployment-manager-tutorial.md)
