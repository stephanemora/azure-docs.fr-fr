---
title: Créer une instance d’Apache Kafka pour Confluent Cloud à l’aide d’Azure CLI - Solutions partenaires Azure
description: Cet article explique comment utiliser Azure CLI afin de créer une instance d’Apache Kafka pour Confluent Cloud.
ms.service: partner-services
ms.topic: quickstart
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a9c49b1a19cbed080255492b90554ce0b138c3bd
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112534791"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-cli"></a>Démarrage rapide : Bien démarrer avec Apache Kafka pour Confluent Cloud - Azure CLI

Dans ce guide de démarrage rapide, vous allez utiliser la Place de marché Azure et Azure CLI afin de créer une instance d’Apache Kafka pour Confluent Cloud.

## <a name="prerequisites"></a>Prérequis

- Un compte Azure. Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free/).
- Vous devez disposer du rôle _Propriétaire_ ou _Contributeur_ pour votre abonnement Azure. L’intégration entre Azure et Confluent ne peut être configurée que par les utilisateurs ayant un accès _Propriétaire_ ou _Contributeur_. Avant de commencer, [veillez à disposer de l’accès approprié](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Rechercher une offre

Utilisez le portail Azure pour rechercher l’application Apache Kafka pour Confluent Cloud.

1. Dans un navigateur web, accédez au [portail Azure](https://portal.azure.com/) et connectez-vous à votre compte.

1. Si vous avez visité la **Place de marché** dans une session récente, sélectionnez l’icône parmi les options disponibles. Dans le cas contraire, recherchez _Place de marché_.

    :::image type="content" source="media/marketplace.png" alt-text="Icône de la Place de marché.":::

1. Dans la page **Place de marché**, vous disposez de deux options en fonction du type de plan souhaité. Vous pouvez vous inscrire à un plan de paiement à l’utilisation ou un plan d’engagement. Le paiement à l’utilisation est disponible pour tout le monde. Le plan d’engagement est disponible pour les clients qui ont été approuvés pour une offre privée.

   - Pour les clients inscrits à un plan de **paiement à l’utilisation**, recherchez _Apache Kafka sur Confluent Cloud_. Sélectionnez l’offre pour Apache Kafka sur Confluent Cloud.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="rechercher parmi les offres de la Place de marché Azure.":::

   - Pour les clients inscrits à un plan d’**engagement**, sélectionnez le lien vers **Voir les offres privées**. L’engagement vous oblige à vous inscrire pour un montant minimal de dépense. Utilisez cette option uniquement lorsque vous savez que vous avez besoin du service pendant une période prolongée.

     :::image type="content" source="media/view-private-offers.png" alt-text="voir les offres privées.":::

     Recherchez _Apache Kafka sur Confluent Cloud_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="sélectionner une offre privée.":::

## <a name="create-resource"></a>Créer une ressource

Une fois que vous avez sélectionné l’offre pour Apache Kafka sur Confluent Cloud, vous êtes prêt à configurer l’application.

Commencez par préparer votre environnement pour Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Après vous être connecté, utilisez la commande [az confluent organization create](/cli/azure/confluent/organization#az_confluent_organization_create) pour créer la nouvelle ressource d’organisation :

```azurecli
az confluent organization create --name "myOrganization" --resource-group "myResourceGroup" \
 --location "my location" \ 
 --offer-detail id="string" plan-id="string" plan-name="string" publisher-id="string" term-unit="string" \ 
 --user-detail email-address="contoso@microsoft.com" first-name="string" last-name="string" \ 
 --tags Environment="Dev" 
```

> [!NOTE]
> Si vous souhaitez que le résultat de la commande soit retourné avant la fin de l’opération de création, ajoutez le paramètre facultatif `--no-wait`. L’opération continue à s’exécuter jusqu’à ce que l’organisation Confluent soit créée.
 
Pour suspendre l’exécution de l’interface CLI jusqu’à ce qu’une condition ou un événement spécifique se produise dans l’organisation, utilisez la commande [az confluent organization wait](/cli/azure/confluent/organization#az_confluent_organization_wait). Par exemple, pour attendre la création d’une organisation :

```azurecli
az confluent organization wait --name "myOrganization" --resource-group "myResourceGroup" --created
```

Pour afficher la liste des organisations existantes, utilisez la commande [az confluent organization list](/cli/azure/confluent/organization#az_confluent_organization_list).

Vous pouvez afficher toutes les organisations de votre abonnement :

```azurecli
az confluent organization list
```

Vous pouvez également afficher les organisations d’un groupe de ressources :

```azurecli
az confluent organization list --resource-group "myResourceGroup"
```

Pour afficher les propriétés d’une organisation spécifique, utilisez la commande [az confluent organization show](/cli/azure/confluent/organization#az_confluent_organization_show).

Vous pouvez afficher l’organisation à l’aide de son nom :

```azurecli
az confluent organization show --name "myOrganization" --resource-group "myResourceGroup"
```

Vous pouvez également afficher l’organisation à l’aide de son ID de ressource :

```azurecli
az confluent organization show --ids "/subscriptions/{SubID}/resourceGroups/{myResourceGroup}/providers/Microsoft.Confluent/organizations/{myOrganization}"
```

Si vous obtenez une erreur, consultez [Résolution des problèmes des solutions Apache Kafka pour Confluent Cloud](troubleshoot.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer la ressource Confluent Cloud](manage.md)
