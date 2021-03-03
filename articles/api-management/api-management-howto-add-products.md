---
title: Tutoriel - Créer et publier un produit dans Gestion des API Azure
description: Dans ce tutoriel, vous créez et vous publiez un produit dans Gestion des API Azure. Une fois qu’il est publié, les développeurs peuvent commencer à utiliser ses API.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: d0420b92fc94e0a1a9c8a4057f419a57a9909223
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "100545154"
---
# <a name="tutorial-create-and-publish-a-product"></a>Tutoriel : Créer et publier un produit  

Dans Gestion des API Azure, un [*produit*](api-management-terminology.md#term-definitions) contient une ou plusieurs API, ainsi qu’un quota et des conditions d’utilisation. Une fois le produit publié, les développeurs peuvent s'y abonner et commencer à utiliser ses API.  

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer et publier un produit
> * Ajouter une API au produit

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="Produits de Gestion des API dans le portail":::


## <a name="prerequisites"></a>Prérequis

+ Apprenez la [terminologie relative à Gestion des API Azure](api-management-terminology.md).
+ Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Effectuez également toutes les étapes du tutoriel suivant : [Importer et publier votre première API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Créer et publier un produit

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au portail Azure et accédez à votre instance de Gestion des API.
1. Dans le volet de navigation gauche, sélectionnez **Produits** >  **+ Ajouter**.
1.  Dans la fenêtre **Ajouter un produit**, entrez les valeurs décrites dans le tableau suivant pour créer votre produit.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Ajouter un produit dans le portail":::

    | Nom                     | Description                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nom complet             | Le nom qui doit apparaître dans le [portail des développeurs](api-management-howto-developer-portal.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Description              | Spécifiez des informations sur le produit, comme son objectif, les API auxquelles il donne accès ainsi que d’autres informations détaillées.                                                                                                                                               |
    | State                    | Sélectionnez **Publié** si vous voulez publier le produit. Avant de pouvoir appeler les API dans un produit, ce produit doit être publié. Par défaut, les nouveaux produits sont non publiés et ne sont visibles que par le groupe **Administrateurs**.                                                                                      |
    | Nécessite un abonnement    | Sélectionnez cette option si un utilisateur doit s’abonner pour utiliser le produit.                                                                                                                                                                                                                                   |
    | Nécessite une approbation        | Sélectionnez cette option si vous voulez qu’un administrateur révise et accepte ou refuse les tentatives d’abonnement à ce produit. Si cette option n’est pas sélectionnée, les tentatives d’abonnement sont automatiquement approuvées.                                                                                                                         |
    | Limite du nombre d’abonnements | Si vous le souhaitez, limitez le nombre d’abonnements simultanés.                                                                                                                                                                                                                                |
    | Conditions légales              | Vous pouvez inclure les conditions d’utilisation que les abonnés doivent accepter pour pouvoir utiliser le produit.                                                                                                                                                                                                             |
    | API                     | Sélectionnez une ou plusieurs API. Vous pouvez aussi ajouter des API après avoir créé le produit. Pour plus d’informations, consultez [Ajouter des API à un produit](#add-apis-to-a-product) plus loin dans cet article. |

3. Sélectionnez **Créer** pour créer le produit.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour commencer avec Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Pour créer un produit, exécutez la commande [az apim product create](/cli/azure/apim/product#az_apim_product_create) :

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

Vous pouvez spécifier différentes valeurs pour votre produit :

   | Paramètre | Description |
   |-----------|-------------|
   | `--product-name` | Le nom qui doit apparaître dans le [portail des développeurs](api-management-howto-developer-portal.md). |
   | `--description`  | Spécifiez des informations sur le produit, comme son objectif, les API auxquelles il donne accès ainsi que d’autres informations détaillées. |
   | `--state`        | Sélectionnez **Publié** si vous voulez publier le produit. Avant de pouvoir appeler les API dans un produit, ce produit doit être publié. Par défaut, les nouveaux produits sont non publiés et ne sont visibles que par le groupe **Administrateurs**. |
   | `--subscription-required` | Sélectionnez cette option si un utilisateur doit s’abonner pour utiliser le produit. |
   | `--approval-required` | Sélectionnez cette option si vous voulez qu’un administrateur révise et accepte ou refuse les tentatives d’abonnement à ce produit. Si cette option n’est pas sélectionnée, les tentatives d’abonnement sont automatiquement approuvées. |
   | `--subscriptions-limit` | Si vous le souhaitez, limitez le nombre d’abonnements simultanés.|
   | `--legal-terms`         | Vous pouvez inclure les conditions d’utilisation que les abonnés doivent accepter pour pouvoir utiliser le produit. |

Pour voir vos produits actifs, utilisez la commande [az apim product list](/cli/azure/apim/product#az_apim_product_list) :

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Vous pouvez supprimer un produit en utilisant la commande [az apim product delete](/cli/azure/apim/product#az_apim_product_delete) :

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

### <a name="add-more-configurations"></a>Ajouter des configurations

Poursuivez la configuration du produit après l’avoir enregistré. Dans votre instance Gestion des API, sélectionnez le produit dans la fenêtre **Produits**. Ajouter ou mettre à jour :

|Élément   |Description  |
|---------|---------|
|Paramètres     |    Métadonnées et état du produit     |
|API     |  API associées au produit       |
|[Stratégies](api-management-howto-policies.md)     |  Stratégies appliquées aux API du produit      |
|Contrôle d’accès     |  Visibilité du produit pour les développeurs ou les invités       |
|[Abonnements](api-management-subscriptions.md)    |    Abonnés au produit     |

## <a name="add-apis-to-a-product"></a>Ajout d’API à un produit

Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure un certain nombre d’API et les proposer aux développeurs dans le portail des développeurs. Lors de la création du produit, vous pouvez ajouter une ou plusieurs API existantes. Vous pouvez aussi ajouter des API au produit ultérieurement, à partir de la page **Paramètres** des produits ou lors de la création d’une API.

Les développeurs doivent s’abonner à un produit pour obtenir l’accès à l’API. Quand ils s’abonnent à un produit, ils obtiennent une clé d’abonnement qui est valable pour toutes les API de ce produit. Si vous avez créé l’instance APIM, vous êtes abonné à chaque produit par défaut, car vous êtes déjà administrateur.

### <a name="add-an-api-to-an-existing-product"></a>Ajouter une API à un produit existant

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le volet de navigation gauche de votre instance du service Gestion des API, sélectionnez **Produits**.
1. Sélectionnez un produit, puis sélectionnez **API**.
1. Sélectionnez **Ajouter**.
1. Sélectionnez une ou plusieurs API, puis **Sélectionner**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="Ajouter une API à un produit existant":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Pour voir vos API gérées, utilisez la commande [az apim api list](/cli/azure/apim/api#az_apim_api_list) :

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. Pour ajouter une API à votre produit, exécutez la commande [az apim product api add](/cli/azure/apim/product/api#az_apim_product_api_add) :

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. Vérifiez l’ajout en utilisant la commande [az apim product api list](/cli/azure/apim/product/api#az_apim_product_api_list) :

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

Vous pouvez supprimer une API en utilisant la commande [az apim product api delete](/cli/azure/apim/product/api#az_apim_product_api_delete) :

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

> [!TIP]
> Vous pouvez créer ou mettre à jour l’abonnement de l’utilisateur à un produit avec des clés d’abonnement personnalisées via une [API REST](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) ou une commande PowerShell.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer et publier un produit
> * Ajouter une API au produit

Passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Créer une API vide et simuler des réponses de l’API](mock-api-responses.md)
