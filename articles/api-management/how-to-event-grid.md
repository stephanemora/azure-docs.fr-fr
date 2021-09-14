---
title: Envoyer les événements Gestion des API Azure à Event Grid
description: Dans ce guide de démarrage rapide, vous activez les événements Event Grid pour votre instance Gestion des API Azure, puis vous envoyez des événements à un exemple d’application.
author: dlepow
ms.topic: how-to
ms.service: api-management
ms.author: danlep
ms.date: 07/12/2021
ms.custom: ''
ms.openlocfilehash: 9e81fbe26c8b98a0694789567cef9126d5ca02fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563124"
---
# <a name="send-events-from-api-management-to-event-grid-preview"></a>Envoyer les événements Gestion des API à Event Grid (préversion)

La Gestion des API s’intègre à [Azure Event Grid](../event-grid/overview.md) pour vous permettre d’envoyer des notifications d’événements à d’autres services et de déclencher des processus en aval. Event Grid est un service de routage d’événements complètement managé qui utilise un modèle de publication-abonnement. Event Grid offre une prise en charge intégrée des services Azure comme [Azure Functions](../azure-functions/functions-overview.md) et [Azure Logic Apps](../logic-apps/logic-apps-overview.md), et peut fournir des alertes d’événements à des services non-Azure à l’aide de webhooks.

Par exemple, à l’aide de l’intégration avec Event Grid, vous pouvez créer une application qui met à jour une base de données, crée un compte de facturation et envoie un e-mail de notification chaque fois qu’un utilisateur est ajouté à votre instance Gestion des API.

Dans cet article, vous vous abonnez aux événements Event Grid dans votre instance de Gestion des API, déclenchez des événements et envoyez les événements à un point de terminaison qui traite les données. En termes simples, vous envoyez des événements à un exemple d’application web qui collecte et affiche les messages :

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-intro.png" alt-text="Événements Gestion des API dans la visionneuse Event Grid":::

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
- Si vous ne disposez pas déjà d’un service Gestion des API, suivez le guide de démarrage rapide : [Créer une instance Gestion des API Azure](get-started-create-service-instance.md)
- Activez une[identité managée affectée par le système](api-management-howto-use-managed-service-identity.md#create-a-system-assigned-managed-identity) dans votre instance de Gestion des API.
- Créez un [groupe de ressources](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) si vous n’en avez pas un dans lequel déployer l’exemple de point de terminaison.

## <a name="create-an-event-endpoint"></a>Créer un point de terminaison d’événements

Dans cette section, vous utilisez un modèle Resource Manager pour déployer un exemple d’application web prédéfini sur Azure App Service. Plus tard, vous vous abonnez aux événements Event Grid de votre instance Gestion des API et vous spécifiez cette application comme point de terminaison auquel envoyer les événements.

Pour déployer cet exemple d’application, vous pouvez utiliser l’interface Azure CLI, Azure PowerShell ou le portail Azure. L’exemple suivant utilise la commande [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) dans l’interface Azure CLI.

* Définissez `RESOURCE_GROUP_NAME` en spécifiant le nom d’un groupe de ressources existant.
* Définissez `SITE_NAME` en spécifiant un nom unique pour votre application web.

  Le nom du site doit être unique au sein d’Azure, car il fait partie du nom de domaine complet (FQDN) de l’application web. Dans une section ultérieure, vous accédez au nom de domaine complet de l’application dans un navigateur web pour visualiser les événements.

```azurecli-interactive
RESOURCE_GROUP_NAME=<your-resource-group-name>
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Une fois que le déploiement a réussi (il peut prendre quelques minutes), ouvrez un navigateur et accédez à votre application web pour vérifier qu’elle est en cours d’exécution :

`https://<your-site-name>.azurewebsites.net`

Vous devez voir l’exemple d’application sans aucun message d’événement affiché.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-api-management-events"></a>S’abonner aux événements Gestion des API

Dans Event Grid, vous vous abonnez à un *sujet* pour lui indiquer les événements qui vous intéressent, et où les envoyer. Ici, vous créez un abonnement aux événements dans votre instance Gestion des API.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance APIM.
1. Sélectionnez **Événements (préversion) > + Abonnement à un événement**. 
1. Sous l’onglet **Informations de base** :
    * Entrez un **nom** descriptif pour l’abonnement à l’événement.
    * Dans **Types d’événements**, sélectionnez un ou plusieurs types d’événements Gestion des API à envoyer à Event Grid. Pour l’exemple de cet article, sélectionnez au moins **Microsoft.APIManagement.ProductCreated**. 
    * Dans **Détails des points de terminaison**, sélectionnez le type d’événement **Webhook**, cliquez sur **Sélectionner un point de terminaison**, puis entrez l’URL de votre application web, suivie de `api/updates`. Exemple : `https://myapp.azurewebsites.net/api/updates`.
    * Sélectionnez **Confirmer la sélection**.
1. Laissez les paramètres sous les autres onglets à leurs valeurs par défaut, puis sélectionnez **Créer**.

    :::image type="content" source="media/how-to-event-grid/create-event-subscription.png" alt-text="Créer un abonnement aux événements dans le portail Azure":::

## <a name="trigger-and-view-events"></a>Déclencher et afficher les événements

Maintenant que l’exemple d’application est opérationnel et que vous êtes abonné à votre instance Gestion des API avec Event Grid, vous êtes prêt à générer des événements.

Par exemple, [créez un produit](./api-management-howto-add-products.md) dans votre instance Gestion des API. Si votre abonnement aux événements comprend l’événement **Microsoft.APIManagement.ProductCreated**, la création du produit déclenche un événement qui est envoyé (push) à votre point de terminaison d’application web. 

Accédez à votre application web de visionneuse Event Grid et vous devriez voir l’événement `ProductCreated`. Sélectionnez le bouton à côté de cet événement pour voir les détails. 

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-product-created.png" alt-text="Événement créé du produit dans la visionneuse Event Grid":::

## <a name="event-grid-event-schema"></a>Schéma d’événement Event Grid

Les données d’événement Gestion des API incluent `resourceUri`, qui identifie la ressource Gestion des API qui a déclenché l’événement. Pour obtenir des détails sur le schéma de message d’événement Gestion des API, consultez la documentation Event Grid :

[Schéma des événements Azure Event Grid pour la Gestion des API](../event-grid/event-schema-api-management.md)

## <a name="next-steps"></a>Étapes suivantes

* [Choisissez entre les trois services de messagerie Azure - Event Grid, Event Hubs et Service Bus](../event-grid/compare-messaging-services.md)
* Apprenez-en davantage sur l’[abonnement aux événements](../event-grid/subscribe-through-portal.md).