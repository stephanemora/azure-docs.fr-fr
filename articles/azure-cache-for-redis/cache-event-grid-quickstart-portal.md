---
title: 'Démarrage rapide : Router les événements Azure Cache pour Redis vers le point de terminaison web avec le portail Azure'
description: Utilisez Azure Event Grid pour vous abonner aux événements Azure Cache pour Redis, envoyer les événements à un Webhook et prendre en charge les événements dans une application web
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
author: curib
ms.author: cauribeg
ms.openlocfilehash: 5bdd6b0e6f97f7e5a738ab17d68282cf402004b0
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056485"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Démarrage rapide : Router les événements Azure Cache pour Redis vers le point de terminaison web avec le portail Azure

Azure Event Grid est un service de gestion d’événements pour le cloud. Dans ce guide de démarrage rapide, vous allez utiliser le portail Azure pour créer une instance d’Azure Cache pour Redis, vous abonner aux événements de cette instance, déclencher un événement et voir les résultats. En règle générale, vous envoyez des événements à un point de terminaison qui traite les données d’événement et entreprend des actions. Toutefois, pour simplifier ce guide de démarrage rapide, vous allez envoyer des événements à une application web qui va collecter et afficher les messages. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Une fois que vous aurez fini, vous verrez que les données d’événement ont été envoyées à l’application web.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Mise à l’échelle de la visionneuse Azure Event Grid au format JSON.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Créer une instance de cache Azure Cache pour Redis 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Créer un point de terminaison de message

Avant de nous abonner aux événements de l’instance de cache, créons le point de terminaison du message d’événement. En règle générale, le point de terminaison entreprend des actions en fonction des données d’événement. Pour simplifier ce guide de démarrage rapide, vous allez déployer une [application web prédéfinie](https://github.com/Azure-Samples/azure-event-grid-viewer), qui affiche les messages d’événement. La solution déployée comprend un plan App Service, une offre App Service Web Apps et du code source en provenance de GitHub.

1. Sélectionnez **Déployer sur Azure** dans le fichier README de GitHub pour déployer la solution sur votre abonnement. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Bouton Déployer sur Azure.":::

2. Dans la page **Déploiement personnalisé**, procédez comme suit : 
    1. Pour **Groupe de ressources**, sélectionnez le groupe de ressources que vous avez créé au moment où vous avez créé l’instance de cache. Il sera plus facile pour vous de nettoyer une fois que vous aurez terminé le tutoriel en supprimant le groupe de ressources.  
    2. Pour **Nom du site**, entrez un nom pour l’application web.
    3. Pour **Nom du plan d’hébergement**, entrez un nom pour le plan App Service à utiliser pour l’hébergement de l’application web.
    4. Cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**. 
    5. Sélectionnez **Achat**. 
    
    | Paramètre      | Valeur suggérée  | Description |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Dans la liste déroulante, sélectionnez votre abonnement. | Abonnement pour lequel créer cette application web. | 
    | **Groupe de ressources** | Dans la liste déroulante, sélectionnez un groupe de ressources ou choisissez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. | 
    | **Nom du site** | Entrez le nom de votre application web. | Cette valeur ne peut pas être vide. | 
    | **Nom du plan d’hébergement** | Entrez le nom du plan App Service à utiliser pour héberger l’application web. | Cette valeur ne peut pas être vide. | 

1. Sélectionnez Alertes (icône représentant une cloche) dans le portail, puis **Accéder au groupe de ressources**. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Notification de déploiement du portail Azure.":::

4. Dans la page **Groupe de ressources**, dans la liste des ressources, sélectionnez l’application web que vous avez créée. Vous voyez également le plan App Service et l’instance de cache dans cette liste. 

5. Dans la page **App Service** de votre application web, sélectionnez l’URL pour accéder au site web. L’URL doit être au format suivant : `https://<your-site-name>.azurewebsites.net`.

6. Confirmez que vous voyez le site, mais qu’aucun événement n’a encore été posté sur celui-ci.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Site de visionneuse Event Grid vide.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>S’abonner à l’instance d’Azure Cache pour Redis

Au cours de cette étape, vous vous abonnez à une rubrique pour indiquer à Event Grid les événements que vous souhaitez suivre, et où les envoyer.

1. Dans le portail, accédez à l’instance de cache que vous avez créée. 
2. Dans la page **Azure Cache pour Redis**, sélectionnez **Événements** dans le menu de gauche. 
3. Sélectionnez **Webhook**. Vous envoyez des événements à votre application de visionneuse, avec un webhook comme point de terminaison. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Page des événements du portail Azure.":::

4. Dans la page **Créer un abonnement aux événements**, entrez ce qui suit : 

    | Paramètre      | Valeur suggérée  | Description |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom** | Entrez un nom pour l’abonnement aux événements. | La valeur doit avoir une longueur comprise entre 3 et 64 caractères. Elle peut contenir uniquement des lettres, des nombres et des tirets. | 
    | **Types d’événement** | Faites défiler la liste vers le bas, puis sélectionnez le ou les types d’événement à envoyer (push) vers votre destination. Pour ce guide de démarrage rapide, nous allons effectuer une mise à l’échelle de notre instance de cache. | La mise à jour corrective, la mise à l’échelle, l’importation et l’exportation sont les options disponibles. | 
    | **Type de point de terminaison** | Sélectionnez **Webhook**. | Gestionnaire d’événements pour la réception de vos événements. | 
    | **Point de terminaison** | Cliquez sur **Sélectionner un point de terminaison**, entrez l’URL de votre application web, ajoutez `api/updates` à l’URL de la page d’accueil (par exemple `https://cache.azurewebsites.net/api/updates`), puis sélectionnez **Confirmer la sélection**. | Il s’agit de l’URL de l’application web que vous avez créée. | 

5. À présent, dans la page **Créer un abonnement aux événements**, sélectionnez **Créer** pour créer l’abonnement aux événements. 

6. Affichez à nouveau votre application web, et notez qu’un événement de validation d’abonnement lui a été envoyé. Sélectionnez l’icône en forme d’œil pour développer les données d’événements. Event Grid envoie l’événement de validation pour que le point de terminaison puisse vérifier qu’il souhaite recevoir des données d’événement. L’application web inclut du code pour valider l’abonnement.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Visionneuse Azure Event Grid.":::

## <a name="send-an-event-to-your-endpoint"></a>Envoyer un événement à votre point de terminaison

Nous allons maintenant déclencher un événement pour voir comment Event Grid distribue le message à votre point de terminaison. Nous allons effectuer une mise à l’échelle de votre instance d’Azure Cache pour Redis.

1. Dans le portail Azure, accédez à votre instance d’Azure Cache pour Redis, puis sélectionnez **Mettre à l’échelle** dans le menu de gauche.

1. Sélectionnez le niveau tarifaire souhaité dans la page **Mettre à l’échelle**, puis cliquez sur **Sélectionner**. 

    Vous pouvez choisir un niveau tarifaire différent avec les restrictions suivantes :
    
    * Vous ne pouvez pas passer d’un niveau de tarification supérieur à un niveau de tarification inférieur.
      * Vous ne pouvez pas passer d’un cache **Premium** à un cache **Standard** ou **De base**.
      * Vous ne pouvez pas passer d’un cache **Standard** à un cache **De base**.
    * Vous pouvez passer d’un cache **De base** à un cache **Standard**, mais vous ne pouvez pas modifier la taille en même temps. Si vous avez besoin d'une taille différente, vous pouvez effectuer ultérieurement une opération de mise à l'échelle vers la taille voulue.
    * Vous ne pouvez pas passer directement d’un cache **De base** à un cache **Premium**. Vous devez commencer par passer du niveau **De base** au niveau **Standard** en une opération de mise à l’échelle, puis du niveau **Standard** au niveau **Premium** en une deuxième opération.
    * Vous ne pouvez pas mettre à l’échelle depuis une taille supérieure vers la taille **C0 (250 Mo)** .
 
    Lorsqu’un cache est mis à l’échelle vers un nouveau niveau tarifaire, le statut **Mise à l’échelle** s’affiche sur le panneau **Cache Azure pour Redis**. Une fois la mise à l’échelle terminée, le statut passe de **Mise à l’échelle** à **En cours d’exécution**.

1. Vous avez déclenché l’événement, et Event Grid a envoyé le message au point de terminaison configuré lors de l’abonnement. Le message au format JSON contient un tableau répertoriant un ou plusieurs événements. Dans l’exemple suivant, le message JSON contient un tableau avec un événement. Quand vous examinez votre application web, vous pouvez noter qu’un événement **ScalingCompleted** a été reçu. 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Mise à l’échelle de la visionneuse Azure Event Grid au format JSON.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous comptez continuer à utiliser cet événement, ne supprimez pas les ressources créées dans ce guide de démarrage rapide. Sinon, supprimez les ressources que vous avez créées dans ce guide de démarrage rapide.

Sélectionnez le groupe de ressources, puis **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez créer des rubriques et des abonnements d’événements personnalisés, vous pouvez en apprendre davantage sur Event Grid et ce qu’il peut vous offrir :

- [Réaction aux événements Azure Cache pour Redis](cache-event-grid.md)
- [À propos d’Event Grid](../event-grid/overview.md)

