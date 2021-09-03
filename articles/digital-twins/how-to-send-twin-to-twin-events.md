---
title: Configurer la gestion des événements entre jumeaux
titleSuffix: Azure Digital Twins
description: Découvrez comment créer une fonction dans Azure pour propager des événements dans le graphique jumeau.
author: baanders
ms.author: baanders
ms.date: 8/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 68229c648968711bf65c0870c2fb38903376b1d6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534778"
---
# <a name="set-up-twin-to-twin-event-handling-with-azure-functions"></a>Configurer la gestion des événements entre jumeaux à l’aide d’Azure Functions

Un graphique Azure Digital Twins entièrement connecté est piloté par la propagation d’événements. Les données arrivent dans Azure Digital Twins à partir de sources externes comme IoT Hub, puis sont propagées par le biais du graphique Azure Digital Twins, en mettant à jour les jumeaux appropriés, le cas échéant.

Par exemple, imaginez un graphique représentant les étages et les pièces dans un immeuble, où chaque étage contient plusieurs pièces. Vous souhaiterez peut-être configurer un flux de données entre jumeaux de manière à ce que chaque fois que la propriété de température sur un jumeau de la pièce est mise à jour, une nouvelle température moyenne est calculée pour toutes les pièces au même étage, et la propriété de température du jumeau de l’étage est mise à jour pour refléter la nouvelle température moyenne dans toutes les pièces qu’il contient (y compris celle qui a été mise à jour). 

Dans cet article, vous allez apprendre à envoyer des événements entre jumeaux, ce qui vous permet de mettre à jour des jumeaux en réponse à des modifications de propriété ou à d’autres données à partir d’un autre jumeau dans le graphique. Actuellement, les mises à jour entre jumeaux sont gérées par la configuration d’une [fonction Azure](../azure-functions/functions-overview.md) qui surveille les événements de cycle de vie de jumeau qui doivent affecter d’autres zones du graphique, et apporte des modifications à d’autres jumeaux en conséquence.

## <a name="prerequisites"></a>Configuration requise

Cet article utilise **Visual Studio**. Vous pouvez télécharger la dernière version à partir de la page [Téléchargements de Visual Studio](https://visualstudio.microsoft.com/downloads/).

Pour définir la gestion entre jumeaux, vous aurez besoin d’une **instance Azure Digital Twins** avec laquelle travailler. Pour des instructions sur la façon de créer une instance, consultez [Configurer une instance et l’authentification Azure Digital Twins](./how-to-set-up-instance-portal.md). L’instance doit contenir au moins **deux jumeaux** entre lesquels vous voulez envoyer des données.

De manière facultative, vous pouvez configurer l’[ingestion de télémétrie automatique via IoT Hub](how-to-ingest-iot-hub-data.md) pour vos jumeaux également. Cela n’est pas obligatoire pour envoyer des données de jumeau à jumeau, mais il s’agit d’un élément important d’une solution complète dans laquelle le graphe jumeau est piloté par la télémétrie en direct.

## <a name="set-up-endpoint-and-route"></a>Configurer le point de terminaison et l’itinéraire

Pour configurer la gestion d’événement entre jumeaux, commencez par créer un **point de terminaison** dans Azure Digital Twins et un **itinéraire** pour ce point de terminaison. Les jumeaux en cours de mise à jour utilisent l’itinéraire pour envoyer des informations sur les événements de mise à jour au point de terminaison (où Event Grid peut les sélectionner ultérieurement et les transmettre à une fonction Azure pour traitement).

[!INCLUDE [digital-twins-twin-to-twin-resources.md](../../includes/digital-twins-twin-to-twin-resources.md)]

## <a name="create-the-azure-function"></a>Créer la fonction Azure

Ensuite, créez une fonction Azure qui écoute sur le point de terminaison et reçoit les événements jumeaux qui lui sont envoyés via l’itinéraire. 

1. Tout d’abord, créez un projet Azure Functions dans Visual Studio sur votre machine. Pour obtenir des instructions sur la façon de procéder, consultez [Développer des Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project).

2. Ajoutez les packages suivants à votre projet (vous pouvez utiliser le gestionnaire de package Visual Studio NuGet ou les commandes `dotnet` dans un outil de ligne de commande).

    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid)

3. Renseignez la logique de votre fonction. Vous pouvez afficher un exemple de code de fonction pour plusieurs scénarios dans le référentiel [azure-digital-twins-getting-started](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/azure-functions) pour vous aider à démarrer.

5. Publier l’application de fonction dans Azure Pour obtenir des instructions sur la façon de publier une application de fonction, consultez [Développer Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

### <a name="configure-the-function-app"></a>Configurer l’application de fonction

Pour que votre fonction puisse accéder à Azure Digital Twins, elle a besoin d’informations sur l’instance et l’autorisation pour y accéder. Dans cette section, vous **attribuez un rôle d’accès** à la fonction et **configurez les paramètres de l’application** afin qu’elle puisse accéder à votre instance.

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="connect-the-function-to-event-grid"></a>Connecter la fonction à Event Grid

Ensuite, inscrivez la fonction Azure à la rubrique Event Grid créée précédemment. Cela permet de s’assurer que les données peuvent circuler d’un jumeau mis à jour via la rubrique Event Grid à la fonction.

Pour ce faire, vous créez un **abonnement Event Grid** qui envoie des données à partir de la rubrique Event Grid créée précédemment à votre fonction Azure.

Utilisez la commande CLI suivante, en renseignant les espaces réservés pour votre ID d’abonnement, le groupe de ressources, l’application de fonction et le nom de la fonction.

```azurecli-interactive
az eventgrid event-subscription create --name <name-for-your-event-subscription> --source-resource-id /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.EventGrid/topics/<your-event-grid-topic> \ --endpoint-type azurefunction --endpoint /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.Web/sites/<your-function-app-name>/functions/<function-name> 
```

À présent, votre fonction peut recevoir des événements par le biais de votre rubrique Event Grid. La configuration du flux de données est terminée.

## <a name="test-and-verify-results"></a>Tester et vérifier les résultats

La dernière étape consiste à vérifier que le flux fonctionne, en mettant à jour un jumeau et en vérifiant que les jumeaux concernés sont mis à jour en fonction de la logique dans votre fonction Azure.

Pour lancer le processus, mettez à jour le jumeau qui est la source du flux d’événement. Vous pouvez utiliser l’[Azure CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update), le [kit de développement logiciel (SDK) Azure Digital Twins](how-to-manage-twin.md#update-a-digital-twin) ou les [API REST Azure Digital Twins](how-to-use-postman.md?tabs=data-plane) pour effectuer la mise à jour.

Ensuite, interrogez votre instance Azure Digital Twins pour le jumeau concerné. Vous pouvez utiliser l’[Azure CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) ou les [API REST et le kit de développement logiciel (SDK) Azure Digital Twins](how-to-query-graph.md#run-queries-with-the-api). Vérifiez que le jumeau a reçu les données et les a mises à jour comme prévu.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous configurez la gestion des événements entre jumeaux dans Azure Digital Twins. Ensuite, configurez une fonction Azure pour déclencher automatiquement ce flux en fonction de la télémétrie entrante à partir de périphériques IoT Hub : [Ingérer la télémétrie depuis IoT Hub](how-to-ingest-iot-hub-data.md).
