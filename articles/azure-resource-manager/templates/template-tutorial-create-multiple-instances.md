---
title: Créer plusieurs instances de ressource
description: Découvrez comment créer un modèle Azure Resource Manager pour déployer plusieurs instances de ressources Azure.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9ed14ce1af6421accccface1b66119057d1c5a30
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239292"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Tutoriel : Créer plusieurs instances de ressources grâce à des modèles ARM

Découvrez comment procéder à une itération dans votre modèle Azure Resource Manager (ARM) pour créer plusieurs instances d’une ressource Azure. Dans ce tutoriel, vous modifiez un modèle pour créer trois instances de compte de stockage.

![Azure Resource Manager crée plusieurs instances de diagramme](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Modifier le modèle
> * Déployer le modèle

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Visual Studio Code avec l’extension Outils Resource Manager. Consultez [Utiliser Visual Studio Code pour créer des modèles ARM](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Le dépôt [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/) contient les modèles ARM. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce démarrage rapide se nomme [Créer un compte de stockage standard](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Le modèle définit une ressource de compte de stockage Azure.

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Une ressource « Microsoft.Storage/storageaccounts » est définie dans le modèle. Comparer le modèle à la [référence du modèle](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). Il est préférable de comprendre quelques notions basiques du modèle avant de le personnaliser.
5. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer le fichier sous le nom **azuredeploy.json** sur votre ordinateur local.

## <a name="edit-the-template"></a>Modifier le modèle

Le modèle existant crée un unique compte de stockage. Vous personnalisez le modèle pour créer trois comptes de stockage.

Dans Visual Studio Code, effectuez les quatre modifications suivantes :

![Azure Resource Manager crée plusieurs instances](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Ajoutez un élément `copy` à la définition de ressource du compte de stockage. Dans l’élément copy, vous indiquez le nombre d’itérations et une variable pour cette boucle. La valeur de décompte doit être un entier positif et ne pas dépasser 800.
2. La fonction `copyIndex()` renvoie l’itération actuelle dans la boucle. Vous utilisez l’index en tant que préfixe du nom. `copyIndex()` est basé sur zéro. Pour décaler la valeur d’index, vous pouvez transmettre une valeur dans la fonction copyIndex(). Par exemple, *copyIndex(1)* .
3. Supprimez l’élément **variables** car il n’est plus utilisé.
4. Supprimez l’élément **outputs**. Ce n'est plus nécessaire.

Le modèle complet ressemble à ceci :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Pour plus d’informations sur la création de plusieurs instances, consultez [Déployer plusieurs instances d’une ressource ou d’une propriété dans des modèles ARM](./copy-resources.md)

## <a name="deploy-the-template"></a>Déployer le modèle

Reportez-vous à la section [Déployer le modèle](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) dans le guide de démarrage rapide Visual Studio Code pour connaître la procédure de déploiement.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour répertorier tous les trois comptes de stockage, omettez le paramètre --name :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

Comparez les noms de compte de stockage avec la définition de nom dans le modèle.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer plusieurs instances de compte de stockage.  Dans le tutoriel suivant, vous développez un modèle disposant de plusieurs ressources et types de ressources. Certaines ressources comportent des ressources dépendantes.

> [!div class="nextstepaction"]
> [Créer des ressources dépendantes](./template-tutorial-create-templates-with-dependent-resources.md)
