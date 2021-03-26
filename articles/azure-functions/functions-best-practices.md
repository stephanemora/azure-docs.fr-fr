---
title: Bonnes pratiques pour Azure Functions
description: Découvrez les bonnes pratiques et les modèles pour Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5783f8092a6435b43ab8720df18cc5200e390d46
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100378245"
---
# <a name="best-practices-for-performance-and-reliability-of-azure-functions"></a>Meilleures pratiques pour optimiser les performances et la fiabilité d’Azure Functions

Cet article fournit des instructions pour améliorer les performances et la fiabilité de vos applications de fonction [sans serveur](https://azure.microsoft.com/solutions/serverless/).  

Voici les bonnes pratiques liées à la création et à l’élaboration de vos solutions serverless à l’aide d’Azure Functions.

## <a name="avoid-long-running-functions"></a>Évitez les fonctions dont l’exécution prend beaucoup de longtemps

Ces fonctions peuvent provoquer des problèmes de délai d’attente inattendus. Pour en savoir plus sur les délais d’attente pour un plan d’hébergement donné, consultez [Durée du délai d’attente de l’application de fonction](functions-scale.md#timeout).

Une fonction peut devenir volumineuse en raison des nombreuses dépendances Node.js. L’importation des dépendances peut entraîner une augmentation des temps de chargement aboutissant à des délais d’attente inattendus. Les dépendances sont chargées tant explicitement qu’implicitement. Un module chargé par votre code peut charger ses propres modules supplémentaires.

Autant que possible, subdivisez les fonctions volumineuses en ensembles de fonctions plus petits qui fonctionnent ensemble et retournent des réponses rapides. Par exemple, un webhook ou une fonction de déclenchement HTTP peut nécessiter une réponse avec accusé de réception dans un délai imparti. Il est courant que des webhooks demandent une réponse immédiate. Vous pouvez passer la charge utile du déclencheur HTTP dans une file d’attente en vue de son traitement par une fonction de déclenchement de file d’attente. Cette approche vous permet de différer le travail réel et de retourner une réponse immédiate.

## <a name="cross-function-communication"></a>Communication entre fonctions

Les [Fonctions durables](durable/durable-functions-overview.md) et le service [Azure Logic Apps](../logic-apps/logic-apps-overview.md) sont conçus pour gérer les transitions d’état et la communication entre plusieurs fonctions.

Si vous n’utilisez pas les fonctions durables ni Logic Apps pour l’intégration à plusieurs fonctions, une bonne pratique consiste à utiliser des files d’attente de stockage pour la communication entre les fonctions. La principale raison est que les files d’attente de stockage sont plus économiques et beaucoup plus faciles à configurer que les autres options de stockage.

La taille de chaque message d’une file d’attente de stockage est limitée à 64 Ko. Pour transmettre des messages plus volumineux entre les fonctions, vous pouvez utiliser une file d’attente Azure Service Bus, qui prend en charge des tailles de message allant jusqu’à 256 Ko pour le niveau Standard, 1 Mo pour le niveau Premium.

Les rubriques Service Bus sont utiles si vous avez besoin de filtrer les messages avant de les traiter.

Les hubs d’événements sont utiles pour prendre en charge les communications de volume élevé.

## <a name="write-functions-to-be-stateless"></a>Écrire des fonctions sans état

Les fonctions doivent être sans état et idempotentes si possible. Associez toutes les informations d’état requises à vos données. Par exemple, une commande en cours de traitement aurait probablement un membre `state` associé. Une fonction peut traiter une commande suivant cet état, tandis que la fonction elle-même reste sans état.

Les fonctions idempotentes sont particulièrement recommandées avec les déclencheurs à minuterie. Par exemple, si une tâche doit absolument s’exécuter une fois par jour, écrivez-la de façon à ce qu’elle puisse s’exécuter à n’importe quel moment de la journée avec les mêmes résultats. La fonction peut s’arrêter si aucun travail ne doit être effectué au cours d’un jour donné. En outre, si une exécution précédente a été interrompue, la prochaine exécution doit reprendre au point d’interruption.

## <a name="write-defensive-functions"></a>Écrire des fonctions défensives

Supposons que votre fonction peut être confrontée à une exception à tout moment. Concevez vos fonctions de façon à ce qu’elles puissent reprendre à un point d’échec précédent la prochaine fois qu’elles s’exécutent. Imaginez un scénario qui nécessite les actions suivantes :

1. Récupérer 10 000 lignes d’une base de données.
2. Créer un message de file d’attente pour chacune de ces lignes en vue de leur traitement.

Selon la complexité de votre système, il est possible que des services impliqués en aval se comportent de manière incorrecte, que des pannes réseau se produisent, que des limites de quota soient atteintes, etc. Tous ces facteurs peuvent affecter votre fonction à tout moment. Vous devez concevoir vos fonctions en conséquence.

Comment votre code réagit-il si une défaillance se produit après l’insertion de 5 000 de ces éléments dans une file d’attente en vue de leur traitement ? Suivez les éléments dans un jeu que vous avez terminé. Sinon, vous pouvez les réinsérer la prochaine fois. Cette double insertion peut avoir un impact sérieux sur votre flux de travail, veillez donc à [rendre vos fonctions idempotent](functions-idempotent.md). 

Si un élément de file d’attente a déjà été traité, permettez à votre fonction d’être une absence d’opération.

Tirez parti des mesures défensives déjà fournies pour les composants que vous utilisez dans la plateforme Azure Functions. Par exemple, consultez **Gestion des messages de file d’attente incohérents** dans la documentation relative aux [liaisons et déclencheurs de file d’attente de stockage Azure](functions-bindings-storage-queue-trigger.md#poison-messages).

## <a name="function-organization-best-practices"></a>Bonnes pratiques pour l’organisation des fonctions

Dans le cadre de votre solution, vous pouvez développer et publier plusieurs fonctions. Ces fonctions sont souvent combinées en une application de fonction unique, mais elles peuvent également être exécutées dans des applications de fonction distinctes. Dans les plans d’hébergement Premium et dédié (App Service), plusieurs applications de fonction peuvent également partager les mêmes ressources en s’exécutant dans le même plan. La façon dont vous regroupez vos fonctions et vos applications de fonction peut impacter les performances, la mise à l’échelle, la configuration, le déploiement et la sécurité de votre solution globale. Comme il n’existe pas de règles qui s’appliquent à tous les scénarios, tenez compte des informations contenues dans cette section lors de la planification et du développement de vos fonctions.

### <a name="organize-functions-for-performance-and-scaling"></a>Organiser les fonctions pour les performances et la mise à l’échelle

Chaque fonction que vous créez a une empreinte mémoire. Même si cette empreinte est généralement petite, l’utilisation d’un trop grand nombre de fonctions dans une application de fonction peut entraîner un ralentissement du démarrage de votre application sur les nouvelles instances. Cela signifie également que l’utilisation globale de la mémoire de votre application de fonction peut être plus élevée. Il est difficile de savoir combien de fonctions doivent résider dans une même application ; cela dépend de votre charge de travail. Toutefois, si votre fonction stocke beaucoup de données en mémoire, envisagez d’avoir moins de fonctions dans une même application.

Si vous exécutez plusieurs applications de fonction dans un même plan Premium ou dédié (App Service), ces applications sont toutes mises à l’échelle ensemble. Si une application de fonction demande plus de mémoire que les autres, elle utilise une quantité disproportionnée de ressources mémoire sur chaque instance sur laquelle elle est déployée. Dans la mesure où cela peut réduire la mémoire disponible pour les autres applications sur chaque instance, vous pouvez exécuter une application de fonction gourmande en mémoire comme celle-ci dans son propre plan d’hébergement.

> [!NOTE]
> Quand vous utilisez le [plan Consommation](./functions-scale.md), nous vous recommandons de toujours placer chaque application dans son propre plan, car les applications sont mises à l’échelle de manière indépendante.

Déterminez si vous souhaitez regrouper des fonctions présentant des profils de charge différents. Par exemple, si vous avez une fonction qui traite des milliers de messages de file d’attente et une autre qui n’est appelée qu’occasionnellement, mais qui a des besoins en mémoire élevés, vous pouvez les déployer sur des applications de fonction distinctes afin qu’elles obtiennent leur propre ensemble de ressources et qu’elles soient mises à l’échelle indépendamment l’une de l’autre.

### <a name="organize-functions-for-configuration-and-deployment"></a>Organiser les fonctions pour la configuration et le déploiement

Les applications de fonction ont un fichier `host.json`, qui est utilisé pour configurer le comportement avancé des déclencheurs de fonction et du runtime Azure Functions. Les modifications apportées au fichier `host.json` s’appliquent à toutes les fonctions au sein de l’application. Si des fonctions ont besoin de configurations personnalisées, envisagez de les déplacer vers leur propre application de fonction.

Toutes les fonctions de votre projet local sont déployées ensemble sous la forme d’un ensemble de fichiers sur votre application de fonction dans Azure. Vous devrez peut-être déployer des fonctions individuelles séparément ou utiliser des fonctionnalités telles que les [emplacements de déploiement](./functions-deployment-slots.md) pour certaines fonctions et pas pour d’autres. Dans ces cas, vous devez déployer ces fonctions (dans des projets de code distincts) sur différentes applications de fonction.

### <a name="organize-functions-by-privilege"></a>Organiser les fonctions par privilège

Les chaînes de connexion et d’autres informations d’identification stockées dans les paramètres d’application donnent à toutes les fonctions de l’application de fonction le même ensemble d’autorisations dans la ressource associée. Envisagez de réduire le nombre de fonctions ayant accès à des informations d’identification spécifiques en déplaçant des fonctions qui n’utilisent pas lesdites informations d’identification vers une application de fonction distincte. Vous pouvez toujours utiliser des techniques telles que [le chaînage de fonctions](/learn/modules/chain-azure-functions-data-using-bindings/) pour passer des données entre des fonctions dans différentes applications de fonction.  

## <a name="scalability-best-practices"></a>Bonnes pratiques relatives à l’extensibilité

Il existe un certain nombre de facteurs qui ont un impact sur la façon dont les instances de votre application de fonction se mettent à l’échelle. Les détails sont fournis dans la documentation sur la [mise à l’échelle de fonction](functions-scale.md).  Voici quelques-unes des bonnes pratiques assurant l’extensibilité optimale d’une application de fonction.

### <a name="share-and-manage-connections"></a>Partager et gérer des connexions

Réutilisez les connexions à des ressources externes chaque fois que possible. Consultez [Guide pratique pour gérer les connexions dans Azure Functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Éviter le partage des comptes de stockage

Lorsque vous créez une application de fonction, vous devez l’associer à un compte de stockage. La connexion au compte de stockage est conservée dans le [paramètre d’application AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage).

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Ne pas mélanger code de test et code de production dans la même application de fonction

Les fonctions d’une application de fonction partagent des ressources. Par exemple, la mémoire est partagée. Si vous utilisez une application de fonction en production, n’y ajoutez pas de ressources et de fonctions de test. Cela peut entraîner une surcharge inattendue pendant l’exécution du code de production.

Soyez attentif à ce que vous chargez dans vos applications de fonction en production. La mémoire moyenne est calculée pour chaque fonction au sein de l’application.

Si un assembly partagé est référencé dans plusieurs fonctions .NET, placez-le dans un dossier partagé commun. Sinon, vous pourriez déployer accidentellement plusieurs versions du même binaire qui se comportent différemment d’une fonction à l’autre.

N’utilisez pas la journalisation détaillée dans le code de production, car cela a un impact négatif sur les performances.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Utiliser du code asynchrone tout en évitant de bloquer les appels

La programmation asynchrone est une pratique recommandée, en particulier lorsque des opérations d’E/S bloquantes sont impliquées.

En C#, évitez toujours de référencer la propriété `Result` ou d’appeler la méthode `Wait` sur une instance `Task`. Cette approche peut mener à un épuisement des threads.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Utiliser plusieurs processus Worker

Par défaut, les instances d’hôte des fonctions utilisent un seul processus Worker. Pour améliorer les performances, en particulier avec les runtimes à thread unique comme Python, utilisez [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) pour augmenter le nombre de processus Worker par hôte (10 maximum). Azure Functions essaie ensuite de distribuer uniformément les appels de fonction simultanés à ces différents Workers.

FUNCTIONS_WORKER_PROCESS_COUNT s’applique à chaque hôte créé par Functions lors du scale-out de votre application pour répondre à la demande.

### <a name="receive-messages-in-batch-whenever-possible"></a>Recevoir des messages par lots chaque fois que possible

Certains déclencheurs, tels que Event Hub, permettent la réception d’un lot de messages sur un simple appel.  Le traitement par lot des messages offre de bien meilleures performances.  Vous pouvez configurer la taille maximale de lot dans le fichier `host.json`, comme indiqué dans la [documentation de référence de host.json](functions-host-json.md)

Pour les fonctions C#, vous pouvez modifier le type en tableau d’objets fortement typé.  Par exemple, au lieu de `EventData sensorEvent` la signature de méthode peut être `EventData[] sensorEvent`.  Pour d’autres langages, vous devez définir explicitement la propriété de cardinalité dans votre fichier `function.json` sur `many` afin d’activer le traitement par lot, [comme indiqué ici](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurer les comportements d’hôte pour mieux gérer l’accès concurrentiel

Le fichier `host.json` dans l’application de fonction permet la configuration des comportements de déclencheur et de runtime hôtes.  En plus des comportements de traitement par lot, vous pouvez gérer l’accès concurrentiel d’un certain nombre de déclencheurs. Souvent l’ajustement des valeurs de ces options peut permettre la mise à l’échelle adéquate de chaque instance face aux demandes des fonctions appelées.

Les paramètres dans le fichier host.json s’appliquent à toutes les fonctions de l’application, dans une *instance unique* de la fonction. Par exemple, si vous aviez une application de fonction dotée de deux fonctions HTTP avec une valeur pour les demandes simultanées [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) définie sur 25, une requête à l’un des déclencheurs HTTP serait comptabilisée dans les 25 demandes simultanées partagées.  Si cette application de fonction était redimensionnée à 10 instances, les dix fonctions autoriseraient en réalité 250 demandes simultanées (10 instances * 25 demandes simultanées par instance). 

D’autres options de configuration d’hôte sont consultables [dans l’article Configuration de host.json](functions-host-json.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Guide pratique pour gérer les connexions dans Azure Functions](manage-connections.md)
* [Bonnes pratiques Azure App Service](../app-service/app-service-best-practices.md)
