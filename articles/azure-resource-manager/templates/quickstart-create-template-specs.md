---
title: Créer et déployer une spec de modèle
description: Découvrez comment créer une spec de modèle à partir d’un modèle ARM. Ensuite, déployez la spec de modèle vers un groupe de ressources dans votre abonnement.
author: tfitzmac
ms.date: 08/31/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 47791455c63852bfc6f8a7e3152fabe18d303ecb
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89227727"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Démarrage rapide : Créer et déployer une spec de modèle (préversion)

Ce démarrage rapide montre comment empaqueter un modèle Azure Resource Manager (modèle ARM) dans une [spec de modèle](template-specs.md), puis déployer celle-ci. Votre spec de modèle contient un modèle ARM qui déploie un compte de stockage.

## <a name="prerequisites"></a>Prérequis

Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> La fonctionnalité Specs de modèle est actuellement en préversion. Pour l’utiliser, vous devez [vous inscrire sur la liste d’attente](https://aka.ms/templateSpecOnboarding).
>
> Après réception de l’approbation de la liste d’attente, vous recevez des instructions pour l’installation du module PowerShell en préversion et du module CLI en préversion.

## <a name="create-template-spec"></a>Créer une spec de modèle

La spec de modèle est un nouveau type de ressource nommé **Microsoft.Resources/templateSpecs**. Pour créer votre spec de modèle, vous pouvez utiliser Azure PowerShell, Azure CLI ou un modèle ARM. Quelle que soit l’option, vous avez besoin d’un modèle ARM empaqueté la spec de modèle.

Avec PowerShell et CLI, le modèle ARM est transmis en tant que paramètre à la commande. Avec un modèle ARM, le modèle à empaqueter dans la spec de modèle est incorporé dans la définition de celle-ci.

Ces deux options sont présentées ci-dessous.

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
      -TemplateJsonFile "c:\Templates\azuredeploy.json"
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
    > Dans le modèle incorporé, tous les crochets gauches (ouvrants) doivent être échappés à l’aide d’un deuxième crochet gauche. Utilisez `[[` au lieu de `[`.

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

Vous pouvez maintenant déployer la spec de modèle. Le déploiement de la spec de modèle est identique à celui du modèle que celle-ci contient, à l’exception du fait que vous transmettez l’ID de ressource de la spec de modèle. Vous utilisez les mêmes commandes de déploiement et, si nécessaire, transmettez les valeurs de paramètres pour la spec de modèle.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Créez un groupe de ressources pour accueillir le nouveau compte de stockage.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Obtenez l’ID de ressource de la spec de modèle.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Version.Id
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
