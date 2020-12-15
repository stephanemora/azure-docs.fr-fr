---
title: Créer et déployer une spec de modèle
description: Découvrez comment créer une spec de modèle à partir d’un modèle ARM. Ensuite, déployez la spec de modèle vers un groupe de ressources dans votre abonnement.
author: tfitzmac
ms.date: 12/01/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 03cf2013f1cec9722af5d7e72285d9f11d8a6bc1
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518955"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Démarrage rapide : Créer et déployer une spec de modèle (préversion)

Ce démarrage rapide montre comment empaqueter un modèle Azure Resource Manager (modèle ARM) dans une [spec de modèle](template-specs.md), puis déployer celle-ci. Votre spec de modèle contient un modèle ARM qui déploie un compte de stockage.

## <a name="prerequisites"></a>Prérequis

Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> La fonctionnalité Specs de modèle est actuellement en préversion. Pour l’utiliser avec Azure PowerShell, vous devez installer la [version 5.0.0 ou ultérieure](/powershell/azure/install-az-ps). Pour l’utiliser avec Azure CLI, utilisez la [version 2.14.2 ou ultérieure](/cli/azure/install-azure-cli).

## <a name="create-template-spec"></a>Créer une spec de modèle

La spec de modèle est un type de ressource nommé **Microsoft.Resources/templateSpecs**. Pour créer votre spec de modèle, vous pouvez utiliser le portail Azure, Azure PowerShell, Azure CLI ou un modèle ARM. Quelle que soit l’option, vous avez besoin d’un modèle ARM empaqueté la spec de modèle.

Avec PowerShell et CLI, le modèle ARM est transmis sous forme de paramètre à la commande. Avec un modèle ARM, le modèle à empaqueter dans la spec de modèle est incorporé dans la définition de celle-ci.

Ces deux options sont présentées ci-dessous.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans la partie supérieure de l’écran, dans **Rechercher des ressources, services et documents**, entrez **spécifications de modèle**, puis sélectionnez **Spécifications de modèle**.
1. Sélectionnez **Créer une spécification de modèle**.
1. Sélectionnez ou entrez les valeurs suivantes :

    - **Nom** : entrez un nom pour la spec de modèle.  Par exemple, **storageSpec**.
    - **Abonnement** : sélectionnez l’abonnement Azure utilisé pour créer la spec de modèle.
    - **Groupe de ressources** : sélectionnez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources.  Par exemple, **templateSpecRG**.
    - **Emplacement** : sélectionnez un emplacement pour le groupe de ressources. Par exemple, **USA Ouest 2**.
    - **Version** : entrez une version pour la spec de modèle. Par exemple, **1.0** ou **v1.0**.

1. Sélectionnez **Suivant : Modifier le modèle**.
1. Remplacez le contenu du modèle par le JSON suivant :

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

    Il s’agit du modèle qui sera empaqueté dans la spec de modèle.
1. Sélectionnez **Vérifier + créer**.
1. Sélectionnez **Create** (Créer).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Lorsque vous créez une spec de modèle avec PowerShell, vous pouvez transmettre un modèle local. Copiez le modèle suivant et enregistrez-le localement dans un fichier nommé **azuredeploy.json**. Ce démarrage rapide part du principe que vous avez enregistré dans le chemin d’accès **c:\Templates\azuredeploy.json**, mais vous pouvez utiliser n’importe quel chemin.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Créez un groupe de ressources pour contenir la spec de modèle.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Ensuite, créez la spec de modèle dans ce groupe de ressources. Attribuez le nom **storageSpec** à la nouvelle spec de modèle.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/azure-cli)

1. Quand vous créez une spec de modèle avec CLI, vous pouvez transmettre un modèle local. Copiez le modèle suivant et enregistrez-le localement dans un fichier nommé **azuredeploy.json**. Ce démarrage rapide part du principe que vous avez enregistré dans le chemin d’accès **c:\Templates\azuredeploy.json**, mais vous pouvez utiliser n’importe quel chemin.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Créez un groupe de ressources pour contenir la spec de modèle.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Ensuite, créez la spec de modèle dans ce groupe de ressources. Attribuez le nom **storageSpec** à la nouvelle spec de modèle.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[Modèle ARM](#tab/azure-resource-manager)

1. Lorsque vous utilisez un modèle ARM pour créer la spec de modèle, le modèle est incorporé dans la définition de la ressource. Copiez le modèle suivant et enregistrez-le localement sous **azuredeploy.json**. Ce démarrage rapide part du principe que vous avez enregistré dans le chemin d’accès **c:\Templates\azuredeploy.json**, mais vous pouvez utiliser n’importe quel chemin.

    > [!NOTE]
    > Dans le modèle incorporé, toutes les [expressions de modèle](template-expressions.md) doivent être échappées au moyen d’un deuxième crochet gauche. Utilisez `"[[` au lieu de `"[`. Les tableaux JSON utilisent toujours un seul crochet gauche.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Utilisez Azure CLI ou PowerShell pour créer un groupe de ressources.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Déployez votre modèle avec Azure CLI ou PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Déployer une spec de modèle

Vous pouvez maintenant déployer la spec de modèle. Le déploiement de la spec de modèle est identique au déploiement du modèle que celle-ci contient, si ce n’est que vous transmettez l’ID de ressource de la spec de modèle dans Azure PowerShell ou Azure CLI. Vous utilisez les mêmes commandes de déploiement et, si nécessaire, transmettez des valeurs de paramètres pour la spec de modèle.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Sur le portail Azure, ouvrez le groupe de ressources que vous avez créé au cours de la dernière procédure.  Par exemple, **templateSpecRG**.
1. Sélectionnez la spec de modèle que vous avez créée. Par exemple, **storageSpec**.
1. Sélectionnez **Déployer**.
1. Sélectionnez ou entrez les valeurs suivantes :

    - **Abonnement** : sélectionnez un abonnement Azure pour créer la ressource.
    - **Groupe de ressources**  : sélectionnez **Créer nouveau**, puis entrez **storageRG**.
    - **Type de compte de stockage** : sélectionnez **Standard_GRS**.

    Vous créez un groupe de ressources puis déployez le modèle de la spec de modèle dans le nouveau groupe de ressources.

1. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Créez un groupe de ressources pour accueillir le nouveau compte de stockage.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Obtenez l’ID de ressource de la spec de modèle.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Déployez la spec de modèle.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Vous fournissez les paramètres exactement comme vous le feriez pour un modèle ARM. Redéployez la spec de modèle avec un paramètre pour le type de compte de stockage.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/azure-cli)

1. Créez un groupe de ressources pour accueillir le nouveau compte de stockage.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Obtenez l’ID de ressource de la spec de modèle.

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Il existe un problème connu lié à l’obtention de l’ID de la spec de modèle et à son attribution à une variable dans Windows PowerShell.

1. Déployez la spec de modèle.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Vous fournissez les paramètres exactement comme vous le feriez pour un modèle ARM. Redéployez la spec de modèle avec un paramètre pour le type de compte de stockage.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[Modèle ARM](#tab/azure-resource-manager)

1. Copiez le modèle suivant et enregistrez-le localement dans un fichier nommé **storage.json**.

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Utilisez Azure CLI ou PowerShell pour créer un groupe de ressources pour le compte de stockage.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Déployez votre modèle avec Azure CLI ou PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Accorder l'accès

Si vous voulez permettre à d’autres utilisateurs de votre organisation de déployer votre spec de modèle, vous devez leur accorder un accès en lecture. Vous pouvez attribuer le rôle Lecteur à un groupe de Azure AD pour le groupe de ressources contenant les specs de modèle que vous souhaitez partager. Pour plus d’informations, consultez le [Tutoriel : Accorder à un groupe l’accès aux ressources Azure à l’aide d’Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour nettoyer la ressource que vous avez déployée dans le cadre de ce démarrage rapide, supprimez les deux groupes de ressources que vous avez créés.

1. Dans le portail Azure, sélectionnez Groupe de ressources dans le menu de gauche.

1. Entrez le nom du groupe de ressources (templateSpecRG et storageRG) dans le champ Filtrer par nom.

1. Sélectionnez le nom du groupe de ressources.

1. Sélectionnez Supprimer le groupe de ressources dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir la création d’une spec de modèle incluant des modèles liés, consultez [Créer une spec de modèle d’un modèle lié](template-specs-create-linked.md).
