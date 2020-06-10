---
title: 'Démarrage rapide : Créer un registre géorépliqué - Modèle Resource Manager'
description: Découvrez comment créer un registre de conteneurs Azure géorépliqué à l’aide d’un modèle Azure Resource Manager.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 05/26/2020
ms.openlocfilehash: c94cd3b4b455691e85e7525007fcdf7a056a2b28
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558079"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-a-resource-manager-template"></a>Démarrage rapide : Créer un registre de conteneurs géorépliqué à l’aide d’un modèle Azure Resource Manager

Ce guide de démarrage rapide montre comment créer une instance Azure Container Registry à l’aide d’un modèle Azure Resource Manager. Le modèle configure un registre [géorépliqué](container-registry-geo-replication.md), qui synchronise automatiquement le contenu du registre entre plusieurs régions Azure. La géoréplication permet un accès aux images à proximité du réseau à partir de déploiements régionaux, tout en offrant une expérience de gestion unique. Il s’agit d’une caractéristique du niveau de service de registre [Premium](container-registry-skus.md). 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Aucun.

## <a name="create-a-geo-replicated-registry"></a>Créer un registre géorépliqué

### <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). Le modèle configure un registre et un réplica régional supplémentaire.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json" range="1-81" highlight="45-74" :::

Les ressources suivantes sont définies dans le modèle :

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)**  : création d’un registre de conteneurs Azure
* **[Microsoft.ContainerRegistry/registries/replications](/azure/templates/microsoft.containerregistry/registries/replications)**  : création d’un réplica de registre de conteneurs

Vous trouverez d’autres exemples de modèles Azure Container Registry dans la [galerie de modèles de démarrage rapide](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Déployer le modèle

 1. Cliquez sur l’image ci-après pour vous connecter à Azure et ouvrir un modèle.

    [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 2. Sélectionnez ou entrez les valeurs suivantes.

    * **Abonnement** : sélectionnez un abonnement Azure.
    * **Groupe de ressources** : sélectionnez **Créer**, entrez un nom unique pour le groupe de ressources, puis sélectionnez **OK**.
    * **Emplacement** : sélectionnez un emplacement pour le groupe de ressources. Exemple : **USA Centre**.
    * **Nom d’ACR** : acceptez le nom généré pour le registre ou entrez un nom. Il doit être globalement unique.
    * **Emplacement** : acceptez l’emplacement généré pour le réplica de base du registre ou entrez un emplacement comme **USA Centre**. 
    * **Emplacement du réplica d’ACR** : entrez un emplacement pour le réplica du registre en utilisant le nom abrégé de la région. Il doit être différent de l’emplacement du registre de base. Exemple : **Europe Ouest**.
    * **J’accepte les termes et conditions mentionnés ci-dessus** : cochez la case.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Propriétés du modèle":::

 3. Si vous acceptez les termes et conditions, sélectionnez **Acheter**. Une fois le registre créé avec succès, vous recevez une notification :

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Notification du portail":::

 Le portail Azure est utilisé pour déployer le modèle. Outre le portail Azure, vous pouvez utiliser Azure PowerShell, l’interface Azure CLI et l’API REST. Pour découvrir d’autres méthodes de déploiement, consultez [Déployer des modèles](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Utilisez le portail Azure ou un outil tel que l’interface de ligne de commande Azure pour passer en revue les propriétés du registre de conteneurs.

1. Dans le portail, recherchez Registres de conteneurs, puis sélectionnez le registre de conteneurs que vous avez créé.

1. Sur la page **Vue d’ensemble**, notez le **serveur de connexion** du registre. Utilisez cet URI quand vous utilisez Docker pour étiqueter et envoyer (push) des images à votre registre. Pour plus d’informations,consultez [Transmettre votre première image à l’aide de l’interface de ligne de commande (CLI) Docker](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Vue d’ensemble du registre":::

1. Sur la page **Réplications**, vérifiez les emplacements du réplica de base et du réplica ajouté par le biais du modèle. Si vous le souhaitez, ajoutez des réplicas sur cette page.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Réplications du registre":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez le groupe de ressources, le registre et le réplica de registre quand vous n’en avez plus besoin. Pour ce faire, sélectionnez le groupe de ressources qui contient le registre dans le portail Azure, puis sélectionnez **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce guide de démarrage rapide, vous avez créé un registre Azure Container Registry avec un modèle Resource Manager et vous avez configuré un réplica de registre dans un autre emplacement. Passez à présent au tutoriel sur Azure Container Registry (ACR) pour approfondir vos connaissances.

> [!div class="nextstepaction"]
> [Tutoriels sur Azure Container Registry](container-registry-tutorial-prepare-registry.md)

Pour obtenir un tutoriel pas à pas vous guidant tout au long du processus de création d’un modèle, consultez :

> [!div class="nextstepaction"]
> [Tutoriel : Créer et déployer votre premier modèle Azure Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
