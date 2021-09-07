---
author: baanders
description: Fichier include décrivant la configuration d’une route et d’un point de terminaison Event Grid
ms.service: digital-twins
ms.topic: include
ms.date: 7/21/2021
ms.author: baanders
ms.openlocfilehash: 0311f53845a4ee18bf595d9efe2593d486818fb8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748571"
---
### <a name="create-the-event-grid-topic"></a>Créer la rubrique Event Grid

[Event Grid](../articles/event-grid/overview.md) est un service Azure qui permet de router et de remettre les événements des services Azure à d’autres emplacements au sein d’Azure. Vous pouvez créer une [rubrique Event Grid](../articles/event-grid/concepts.md) pour collecter certains événements d’une source ; les abonnés peuvent ensuite écouter la rubrique pour recevoir les événements au fur et à mesure de leur arrivée.

Dans Azure Cloud Shell, exécutez la commande suivante pour créer une rubrique Event Grid :

```azurecli-interactive
az eventgrid topic create --resource-group <your-resource-group> --name <name-for-your-event-grid-topic> --location <region>
```

Les informations générées par cette commande décrivent la rubrique Event Grid que vous avez créée. Notez le **nom** que vous avez donné à votre rubrique Event Grid, car vous l’utiliserez plus tard.

### <a name="create-the-endpoint"></a>Créer le point de terminaison

Ensuite, créez un point de terminaison Event Grid dans Azure Digital Twins, qui connectera votre instance à votre rubrique Event Grid. Utilisez la commande ci-dessous, en indiquant le nom de votre rubrique Event Grid ainsi que les autres champs d’espace réservé selon les besoins.

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

Les informations générées par cette commande décrivent le point de terminaison que vous avez créé.

Recherchez le champ `provisioningState` dans la sortie, puis vérifiez que sa valeur est « Succeeded ».

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/output-endpoints.png" alt-text="Capture d’écran du résultat de la requête de point de terminaison dans le Cloud Shell du portail Azure, montrant le point de terminaison avec un état provisioningState Réussi.":::

Il peut également indiquer « Approvisionnement », ce qui signifie que le point de terminaison est toujours en cours de création. Si tel est le cas, attendez quelques secondes, puis exécutez la commande suivante pour vérifier l’état du point de terminaison. Répétez l’opération jusqu’à ce que `provisioningState` indique « Succeeded ».

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Enregistrez le **nom** de votre point de terminaison, car vous l’utiliserez plus tard.

### <a name="create-the-route"></a>Créer la route

Vous allez à présent créer une route Azure Digital Twins qui envoie les événements au point de terminaison Event Grid que vous venez de créer. 

Pour ce faire, vous pouvez utiliser la commande CLI suivante (indiquez le nom de votre point de terminaison ainsi que les autres champs d’espace réservé selon les besoins). Cette commande transfère tous les événements qui se produisent dans le graphe de jumeaux. Vous pouvez limiter les événements à des événements spécifiques si vous le souhaitez, à l’aide de [filtres](../articles/digital-twins/how-to-manage-routes.md?tabs=portal%2Cportal2%2Cportal3#filter-events).

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

Les informations générées par cette commande décrivent la route que vous avez créé.

>[!NOTE]
>L’approvisionnement des points de terminaison (de l’étape précédente) doit être terminé pour que vous puissiez configurer un itinéraire des événements qui les utilise. Si la création d’itinéraires échoue parce que les points de terminaison ne sont pas prêts, patientez quelques minutes, puis réessayez.