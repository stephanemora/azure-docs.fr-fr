---
title: Facturation Durable Functions - Azure Functions
description: Découvrez les comportements internes de Durable Functions et son impact sur la facturation pour Azure Functions.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: b79d50adf932bd5c316fbda9d0964eea7c0484ca
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935457"
---
# <a name="durable-functions-billing"></a>Facturation Durable Functions

[Durable Functions](durable-functions-overview.md) suit le même modèle de facturation qu’Azure Functions. Pour plus d’informations, consultez [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Lors de l’exécution de fonctions orchestrator dans le [plan de consommation](../functions-scale.md#consumption-plan) Azure Functions, il est nécessaire de connaître certains comportements de facturation. Les sections suivantes décrivent ces comportements et leur impact plus en détail.

## <a name="orchestrator-function-replay-billing"></a>Facturation de la relecture de fonctions orchestrator

Les [fonctions orchestrator](durable-functions-orchestrations.md) peuvent être relues plusieurs fois pendant toute la durée de vie de l’orchestration. Chaque relecture est considérée par le runtime Azure Functions comme un appel de fonction distinct. C’est pourquoi, dans le plan de consommation Azure Functions, vous êtes facturé pour chaque relecture de la fonction orchestrator. Les autres types de plans ne facturent pas la relecture des fonctions orchestrator.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Attente et interruption dans les fonctions orchestrator

Quand une fonction orchestrator attend qu’une action asynchrone se termine avec `yield` (C#) ou `await` (JavaScript), le runtime considère cette exécution particulière comme terminée. La facturation de la fonction orchestrator s’arrête à ce moment-là et ne reprend pas qu’à la prochaine relecture de la fonction orchestrator. Vous n’êtes pas facturé pour le temps d’attente ou d’interruption dans une fonction orchestrator.

> [!NOTE]
> Les fonctions qui appellent d’autres fonctions sont considérées par certains comme un anti-modèle. Cela est dû à un problème appelé _double facturation_. Quand une fonction appelle une autre fonction directement, elles s’exécutent toutes les deux en même temps. L’appelé exécute activement du code pendant que l’appelant attend une réponse. Dans ce cas, vous devez payer pour le temps passé par l’appelant à attendre que l’appelé s’exécute. Les fonctions orchestrator ne souffrent pas de cette double facturation, car la facturation de la fonction orchestrator s’arrête pendant qu’elle attend le résultat d’une fonction d’activité (ou d’une fonction d’orchestration secondaire).

## <a name="durable-http-polling"></a>Interrogation HTTP durable

Les fonctions orchestrator peuvent effectuer des appels HTTP longs à des points de terminaison externes, comme décrit dans l’article [Fonctionnalités HTTP](durable-functions-http-features.md). La méthode `CallHttpAsync` (C#) et la méthode `callHttp` (JavaScript) peuvent interroger en interne un point de terminaison HTTP quand vous suivez le [modèle 202 asynchrone](durable-functions-http-features.md#http-202-handling). Il n’existe actuellement pas de facturation directe pour les opérations d’interrogation HTTP internes. Toutefois, l’interrogation interne peut entraîner une relecture périodique de la fonction orchestrator et des frais standard vous seront facturés pour ces relectures de fonction internes.

## <a name="azure-storage-transactions"></a>Transactions de stockage Azure

Durable Functions utilise par défaut le Stockage Azure pour rendre l’état persistant, traiter les messages et gérer les partitions par le biais de baux d’objets blob. Comme ce compte de stockage vous appartient, tous les coûts de transaction sont facturés à votre abonnement Azure. Pour plus d’informations sur les artefacts du Stockage Azure utilisés par Durable Functions, consultez l’article [Hubs de tâches](durable-functions-task-hubs.md).

Plusieurs facteurs contribuent aux coûts du Stockage Azure réels engendrés par votre application Durable Functions.

* Une application de fonction est associée à un seul hub de tâches, qui partage un ensemble de ressources de Stockage Azure. Ces ressources sont utilisées par toutes les fonctions Durable Functions dans une application de fonction. Le nombre réel de fonctions dans l’application de fonction n’a aucun impact sur les coûts des transactions du Stockage Azure.
* Chaque instance d’application de fonction interroge en interne plusieurs files d’attente dans le compte de stockage à l’aide d’un algorithme d’interrogation avec interruption exponentielle. Une instance d’application inactive interroge les files d’attente moins fréquemment qu’une application active, ce qui réduit les coûts de transaction. Pour plus d’informations sur le comportement d’interrogation de file d’attente Durable Functions, consultez la [section relative à l’interrogation de file d’attente dans l’article Performances et mise à l’échelle](durable-functions-perf-and-scale.md#queue-polling).
* En cas d’exécution dans les plans Consommation Azure Functions ou Premium, le [contrôleur de mise à l’échelle Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) interroge régulièrement toutes les files d’attente de hub de tâches en arrière-plan. En cas d’échelle faible à modérée, une seule instance de contrôleur d’échelle interroge ces files d’attente. Si l’application de fonction est étendue à un grand nombre d’instances, d’autres instances de contrôleur d’échelle peuvent être ajoutées. Ces instances de contrôleur d’échelle supplémentaires peuvent augmenter les coûts totaux de transaction de file d’attente.
* Chaque instance d’application de fonction est en concurrence pour un ensemble de baux d’objets blob. Ces instances effectuent régulièrement des appels au service BLOB Azure pour renouveler les baux détenus ou tenter d’acquérir de nouveaux baux. Le nombre de baux d’objets blob est déterminé par le nombre de partitions configurées du hub de tâches. Un scale-out vers un plus grand nombre d’instances d’application de fonction augmentera probablement les coûts de transaction du Stockage Azure associés à ces opérations de bail.

Vous trouverez de plus amples sur la tarification du Stockage Azure dans la documentation [Tarification Stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur la tarification Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
