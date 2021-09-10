---
title: Sécuriser le trafic sortant Azure SignalR via des points de terminaison privés partagés
titleSuffix: Azure SignalR Service
description: Comment sécuriser le trafic sortant via des points de terminaison privés partagés pour éviter que le trafic n’aille vers le réseau public.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 07/08/2021
ms.author: dayshen
ms.openlocfilehash: 4be77c9dce68c55a37713c42ceee7fc2b7dbf6d9
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220188"
---
# <a name="secure-azure-signalr-outbound-traffic-through-shared-private-endpoints"></a>Sécuriser le trafic sortant Azure SignalR via des points de terminaison privés partagés

Si vous utilisez le [mode serverless](concept-service-mode.md#serverless-mode) dans Azure SignalR Service, il se peut que vous ayez du trafic sortant vers l’amont. En amont des ressources comme les applications web Azure et Azure Functions peuvent être configurées pour accepter les connexions à partir d’une liste de réseaux virtuels et refuser les connexions externes qui proviennent d’un réseau public. Vous pouvez créer une [connexion privée sortante](../private-link/private-endpoint-overview.md) pour atteindre ces points de terminaison.

   :::image type="content" alt-text="Vue d’ensemble des points de terminaison privés partagés." source="media\howto-shared-private-endpoints\shared-private-endpoint-overview.png" :::

Cette méthode sortante est soumise aux exigences suivantes :

+ En amont, vous devez avoir une application web Azure ou une fonction Azure.

+ Le service Azure SignalR Service doit être au niveau Standard.

+ L’application web Azure ou fonction Azure doit être sur certaines références SKU. Consultez [Utiliser des points de terminaison privés pour une application web Azure](../app-service/networking/private-endpoint.md)

## <a name="shared-private-link-resources-management-apis"></a>API de gestion des ressources de liaison privée partagées

Les points de terminaison privés des ressources sécurisées créées par les API Azure SignalR Service sont appelés *ressources de liaison privée partagées*. En effet, vous « partagez » l’accès à une ressource, comme une fonction Azure, qui a été intégrée au [service Azure Private Link](https://azure.microsoft.com/services/private-link/). Ces points de terminaison privés sont créés dans l’environnement d’exécution d’Azure SignalR Service et ne sont pas directement visibles pour vous.

Pour l’instant, vous pouvez utiliser l’API REST de gestion pour créer ou supprimer des *ressources de liaison privée partagées*. Pour le reste de cet article, nous utiliserons [Azure CLI](/cli/azure/) pour démontrer les appels à l’API REST.

> [!NOTE]
> Les exemples de cet article se basent sur les hypothèses suivantes :
> * L’ID de ressource de ce service Azure SignalR Service est _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr_.
> * L’ID de ressource de la fonction Azure en amont est _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func.

Le reste des exemples montre la façon dont le service _contoso-signalr_ peut être configuré de manière à ce que ses appels en amont à la fonction passent par un point de terminaison privé plutôt que par le réseau public.

### <a name="step-1-create-a-shared-private-link-resource-to-the-function"></a>Étape 1 : Créer une ressource de liaison privée partagée avec la fonction

Vous pouvez effectuer l’appel API suivant avec [Azure CLI](/cli/azure/) pour créer une ressource de liaison privée partagée :

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview --body @create-pe.json
```

Le contenu du fichier *create-pe.json*, qui représente le corps de la requête adressée à l’API, est le suivant :

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve"
      }
}
```

Le processus de création d’un point de terminaison privé sortant est une opération longue (asynchrone). Comme toutes les opérations Azure asynchrones, l’appel de `PUT` ​​renvoie une valeur d’en-tête `Azure-AsyncOperation` semblable à la suivante :

```plaintext
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview"
```

Vous pouvez régulièrement interroger cet URI pour obtenir l’état de l’opération.

Si vous utilisez l’interface CLI, vous pouvez interroger l’état en interrogeant manuellement la valeur `Azure-AsyncOperationHeader`.

```donetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview
```

Attendez que le statut passe à « Succès » avant de passer aux prochaines étapes.

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-function"></a>Étape 2a : Approuver la connexion par point de terminaison privé pour la fonction

> [!NOTE]
> Dans cette section, vous utilisez le portail Azure pour suivre le processus d’approbation d’un point de terminaison privé vers Azure Function. Vous pouvez également utiliser l’[API REST](/rest/api/appservice/web-apps/approve-or-reject-private-endpoint-connection) disponible via le fournisseur de App Service.

> [!IMPORTANT]
> Une fois que vous avez approuvé la connexion au point de terminaison privé, la fonction n’est plus accessible à partir du réseau public. Vous devrez peut-être créer d’autres points de terminaison privés dans votre propre réseau virtuel pour accéder au point de terminaison de la fonction.

1. Dans le portail Azure, sélectionnez l’onglet **Mise en réseau** de votre application de fonction et accédez à **Connexions au point de terminaison privé**. Cliquez sur **Configurer vos connexions de point de terminaison privé**. Une fois l’opération asynchrone réussie, une requête de connexion par point de terminaison privé doit y figurer, avec le message de demande d’appel d’API précédent.

   :::image type="content" alt-text="Capture d’écran du portail Azure, affichant le volet « Connexions de point de terminaison privé »." source="media\howto-shared-private-endpoints\portal-function-approve-private-endpoint.png" :::

1. Sélectionnez le point de terminaison privé créé par Azure SignalR Service. Dans la colonne **point de terminaison privé**, identifiez la connexion de point de terminaison privé par le nom spécifié dans l’API précédente, sélectionnez **Approuver**.

   Assurez-vous que la connexion de points de terminaison privés s’affiche comme indiqué dans la capture d’écran suivante. La mise à jour de l’état dans le portail peut prendre une à deux minutes.

   :::image type="content" alt-text="Capture d’écran du portail Azure, affichant l’état « Approuvé » dans le volet « Connexions de point de terminaison privé »." source="media\howto-shared-private-endpoints\portal-function-approved-private-endpoint.png" :::

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Étape 2b : Interroger l'état de la ressource de liaison privée partagée

La propagation de l’approbation à Azure SignalR Service prend quelques minutes. Pour vérifier que la ressource de liaison privée partagée a bien été mise à jour à l’issue de l’approbation, accédez à son « État de connexion » en utilisant l’API GET.

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview
```

Elle retourne du JSON, où l’état de la connexion s’affiche sous la forme « status » sous la section « properties ».

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve",
        "status": "Approved",
        "provisioningState": "Succeeded"
      }
}

```

Si la propriété Provisioning State (`properties.provisioningState`) de la ressource est `Succeeded` tandis que sa propriété Connection State (`properties.status`) est `Approved`, cela signifie que la ressource de liaison privée partagée est fonctionnelle et qu’Azure SignalR Service peut communiquer via le point de terminaison privé.

### <a name="step-3-verify-upstream-calls-are-from-a-private-ip"></a>Étape 3 : Vérifier que les appels en amont proviennent d’une adresse IP privée

Une fois que le point de terminaison privé est configuré, vous pouvez vérifier que les appels entrants proviennent d’une IP privée en vérifiant l’en-tête `X-Forwarded-For` du côté amont.

:::image type="content" alt-text="Capture d’écran du portail Azure, montrant les demandes entrantes provenant d’une IP privée." source="media\howto-shared-private-endpoints\portal-function-log.png" :::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les points de terminaison privés :

+ [Présentation des points de terminaison privés](../private-link/private-endpoint-overview.md)