---
title: Résoudre les problèmes de dépassement de délai de demande ou HTTP 408 Azure Cosmos DB avec le SDK .NET
description: Comment diagnostiquer et corriger une exception de dépassement de délai de demande avec le SDK .NET
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 09442e01fa160d3851169a51230fa4cbef7e0980
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118567"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Diagnostiquer et résoudre les problèmes liés au dépassement de délai de demande avec le SDK .NET
L’erreur HTTP 408 se produit si le SDK n’a pas pu terminer la demande avant la limite du délai d’attente.

## <a name="customizing-the-timeout-on-the-azure-cosmos-net-sdk"></a>Personnalisation du délai d’expiration sur le Kit de développement logiciel (SDK) .NET Azure Cosmos

Le Kit de développement logiciel (SDK) offre deux alternatives distinctes pour contrôler les délais d’expiration, chacune avec une étendue différente.

### <a name="requesttimeout"></a>RequestTimeout

La configuration `CosmosClientOptions.RequestTimeout` (ou `ConnectionPolicy.RequestTimeout` pour le SDK v2) vous permet de définir un délai d’expiration qui concerne chaque requête réseau individuelle.  Une opération lancée par un utilisateur peut couvrir plusieurs requêtes réseau (par exemple, il peut y avoir une limitation), et cette configuration s’applique à chaque requête réseau lors de la nouvelle tentative. Il ne s’agit pas d’un délai d’expiration de demande d’opération de bout en bout.

### <a name="cancellationtoken"></a>CancellationToken

Toutes les opérations asynchrones dans le Kit de développement logiciel (SDK) ont un paramètre CancellationToken facultatif. [CancellationToken](https://docs.microsoft.com/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) est utilisé tout au long de l’opération, sur toutes les requêtes réseau. Entre les requêtes réseau, CancellationToken peut être vérifié et une opération annulée si le jeton associé a expiré. CancellationToken doit être utilisé pour définir un délai d’expiration approximatif sur l’étendue de l’opération.

> [!NOTE]
> CancellationToken est un mécanisme par lequel la bibliothèque vérifie l’annulation lorsqu’elle [ne provoque pas d’état non valide](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). L’opération peut ne pas être annulée exactement au moment où le délai défini dans l’annulation est écoulé, mais plutôt, une fois le délai écoulé, elle sera annulée lorsqu’il sera possible de le faire en toute sécurité.

## <a name="troubleshooting-steps"></a>Étapes de dépannage
La liste suivante répertorie les causes connues et les solutions pour les exceptions de dépassement de délai de demande.

### <a name="1-high-cpu-utilization-most-common-case"></a>1. Utilisation élevée du processeur (cas le plus courant)
Pour une latence optimale, l’utilisation recommandée du processeur doit être d’environ 40 %. Il est recommandé d’utiliser 10 secondes comme intervalle pour superviser l’utilisation du processeur maximale (non moyenne). Les pics d’utilisation du processeur sont plus courants avec les requêtes entre les partitions où il peut y avoir plusieurs connexions pour une seule requête.

#### <a name="solution"></a>Solution :
L’application cliente qui utilise le SDK doit faire l’objet d’un scale-up/scale-out.

### <a name="2-socket--port-availability-might-be-low"></a>2. La disponibilité de socket/port peut être faible
Quand ils s’exécutent dans Azure, les clients qui utilisent le kit SDK .NET peuvent rencontrer un problème d’épuisement de ports Azure SNAT (PAT).

#### <a name="solution-1"></a>Solution 1 :
Si vous utilisez des machines virtuelles Azure, suivez le [Guide d’épuisement de ports SNAT](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Solution 2 :
Si vous utilisez Azure App Service, suivez le [guide de dépannage des erreurs de connexion](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) et [utilisez les diagnostics App Service](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Solution 3 :
Si vous utilisez Azure Functions, vérifiez que vous suivez la [recommandation Azure Functions](../azure-functions/manage-connections.md#static-clients) de gestion des clients singleton/statiques pour tous les services impliqués (y compris Cosmos DB) et vérifiez les [limites de service](../azure-functions/functions-scale.md#service-limits) en fonction du type et de la taille de votre hébergement Function App.

#### <a name="solution-4"></a>Solution 4 :
Si vous utilisez un proxy HTTP, vérifiez qu’il peut prendre en charge le nombre de connexions configuré dans SDK `ConnectionPolicy`.
Sinon, vous serez confronté à des problèmes de connexion.

### <a name="3-creating-multiple-client-instances"></a>3. Création de plusieurs instances clientes
La création de plusieurs instances clientes peut entraîner des problèmes de conflit et de dépassement de délai de connexion.

#### <a name="solution"></a>Solution :
Suivez les [conseils en lien avec les performances](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage) et utilisez une seule instance CosmosClient pour l’ensemble du processus.

### <a name="4-hot-partition-key"></a>4. Clé de partition à chaud
Azure Cosmos DB répartit le débit global approvisionné de manière uniforme entre les partitions physiques. Lorsqu’il existe une partition à chaud, une ou plusieurs clés de partition logique sur une partition physique consomment la totalité des RU/s de la partition physique, tandis que les RU/s sur d’autres partitions physiques ne sont pas utilisées. Par conséquent, le nombre total de RU/s consommées sera inférieur au nombre global de RU/s provisionnées dans la base de données ou le conteneur, mais vous verrez toujours une limitation (429s) sur les demandes par rapport à la clé de partition logique à chaud. Utilisez la [métrique Consommation d’unités de requête normalisée](monitor-normalized-request-units.md) pour voir si la charge de travail rencontre une partition à chaud. 

#### <a name="solution"></a>Solution :
Choisissez une clé de partition appropriée qui répartit uniformément le volume de demandes et le stockage. Découvrez comment [changer votre clé de partition](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="5-high-degree-of-concurrency"></a>5. Degré élevé de simultanéité
L’application atteint un niveau élevé de simultanéité, ce qui peut entraîner des conflits sur le canal

#### <a name="solution"></a>Solution :
L’application cliente qui utilise le SDK doit faire l’objet d’un scale-up/scale-out.

### <a name="6-large-requests-andor-responses"></a>6. Demandes et/ou réponses volumineuses
Des demandes ou réponses volumineuses peuvent entraîner un blocage en tête de ligne sur le canal et exacerber les conflits, même avec un degré relativement faible de simultanéité.

#### <a name="solution"></a>Solution :
L’application cliente qui utilise le SDK doit faire l’objet d’un scale-up/scale-out.

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. Le taux d’échec est compris dans le contrat de niveau de service Cosmos DB
L’application doit être en mesure de gérer les défaillances temporaires et de réessayer si nécessaire. Il n’y a pas de nouvelle tentative pour les exceptions 408, car il n’est pas possible lors de la création de chemins de savoir si le service a créé ou non l’élément. Le renvoi d’un même élément pour la création entraîne une exception de conflit. La logique métier des applications utilisateur peut avoir une logique personnalisée pour gérer les conflits, ce qui permet d’éviter l’ambiguïté d’un élément existant par rapport à un conflit suite à une nouvelle tentative de création.

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. Le taux d’échec enfreint le contrat de niveau de service Cosmos DB
Contactez le support Azure.

## <a name="next-steps"></a>Étapes suivantes
* [Diagnostiquer et résoudre](troubleshoot-dot-net-sdk.md) les problèmes lors de l’utilisation du SDK .NET Azure Cosmos DB
* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md)
