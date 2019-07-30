---
title: Diagnostiquer et résoudre des problèmes lors de l’utilisation du Kit de développement logiciel (SDK) Azure Cosmos DB
description: Utilisez des fonctionnalités telles que la journalisation côté client et d’autres outils tiers pour identifier, diagnostiquer et résoudre des problèmes liés à Azure Cosmos DB lors de l’utilisation du Kit de développement logiciel .NET.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9511562b81f7ac0c1582897d703f4c5ccb89716
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806395"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostiquer et résoudre des problèmes lors de l’utilisation du Kit de développement logiciel (SDK) Azure Cosmos DB
Cet article traite des problèmes courants, des solutions de contournement, des étapes de diagnostic et des outils quand vous utilisez le [Kit de développement logiciel (SDK) .NET](sql-api-sdk-dotnet.md) avec des comptes d’API SQL Azure Cosmos DB.
Le Kit de développement logiciel (SDK) .NET fournit la représentation logique côté client pour accéder à l’API SQL Azure Cosmos DB. Cet article décrit les outils et les approches qui peuvent vous aider si vous rencontrez des problèmes.

## <a name="checklist-for-troubleshooting-issues"></a>Liste de contrôle pour la résolution des problèmes :
Examinez la liste de contrôle avant de mettre votre application en production. L’utilisation de la liste de contrôle empêche l’affichage de plusieurs problèmes courants. Elle permet également de diagnostiquer rapidement quand un problème se produit :

*   Utilisez le dernier [Kit de développement logiciel (SDK)](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). Le Kit de développement logiciel (SDK) en préversion ne doit pas être utilisé en production. Cela vous évitera de rencontrer des problèmes connus déjà corrigés.
*   Consultez les [conseils relatifs aux performances](performance-tips.md) et suivez les pratiques suggérées. Cela vous aidera à éviter des problèmes de mise à l’échelle, de latence et autres liés aux performances.
*   Activez la journalisation du SDK pour vous aider à résoudre un problème. L’activation de la journalisation pouvant affecter les performances, il est préférable de ne l’activer que lors de la résolution de problèmes. Vous pouvez activer les journaux suivants :
    *   [Journaliser les métriques](monitor-accounts.md) à l’aide du portail Azure. Les métriques du portail présentent la télémétrie d’Azure Cosmos DB, qui est utile pour déterminer si le problème est lié à Azure Cosmos DB ou au client.
    *   Journaliser la [chaîne de diagnostics](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) des réponses à l’opération sur le point.
    *   Journaliser les [métriques de requête SQL](sql-api-query-metrics.md) de toutes les réponses à la requête. 
    *   Suivez la configuration de la [journalisation du Kit de développement logiciel (SDK)]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md).

Jetez un coup d’œil à la section [Problèmes courants et solutions de contournement](#common-issues-workarounds) dans cet article.

Consultez la [section des problèmes GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) qui faisant l’objet d’une surveillance active. Vérifiez si un problème similaire au vôtre dispose déjà d’une solution de contournement. Si vous ne trouvez pas une solution, signalez un incident GitHub. Vous pouvez ouvrir un ticket de support pour des problèmes urgents.


## <a name="common-issues-workarounds"></a>Problèmes courants et solutions de contournement

### <a name="general-suggestions"></a>Recommandations d’ordre général
* Exécutez autant que possible votre application dans la région Azure dans laquelle se trouve votre compte Azure Cosmos DB. 
* Vous pouvez rencontrer des problèmes de connectivité/disponibilité en raison d’un manque de ressources sur votre ordinateur client. Nous vous recommandons de surveiller votre utilisation de l’UC sur les nœuds exécutant le client Azure Cosmos DB et d’adapter leur échelle si leur charge est importante.

### <a name="check-the-portal-metrics"></a>Consulter les métriques du portail
Les [métriques du portail](monitor-accounts.md) vous aident à déterminer si un problème est lié au client ou au service. Par exemple, si les métriques contiennent un taux important de demandes à débit limité (code d’état HTTP 429), ce qui signifie que la demande est limité, voir la section [Taux de demandes trop élevé]. 

### <a name="request-timeouts"></a>Délais d’expiration de demande
Une expiration de demande se produit généralement lors de l’utilisation d’une connexion directe/TCP, mais peut se produire en mode de passerelle. Voici les causes connues courantes et des suggestions sur la façon de résoudre le problème.

* L’utilisation élevée de l’UC entraîne une latence et/ou une expiration de délai de demande. Le client peut faire monter en puissance la machine hôte pour lui octroyer plus de ressources, ou la charge peut être répartie sur davantage de machines.
* La disponibilité de socket/port peut être faible. Lors de l’utilisation de kits de développement logiciel (SDK) .NET antérieurs à la version 2.0, des clients s’exécutant dans Azure pouvaient rencontrer un problème d’[Insuffisance de ports Azure SNAT (PAT)]. Ceci illustre la raison pour laquelle il est recommandé de toujours exécuter la dernière version du Kit de développement logiciel (SDK).
* La création de plusieurs instances de DocumentClient peut entraîner des problèmes de conflit et de dépassement de délai de connexion. Suivez les [conseils en lien avec les performances](performance-tips.md) et utilisez une seule instance DocumentClient pour l’ensemble du processus.
* Les utilisateurs sont parfois confronté à une latence ou à des délais d’attente de demande élevés parce que leurs collections ne sont pas suffisamment approvisionnées, parce que le serveur principal limite les demandes, ou parce que le client effectue de nouvelles tentatives en interne sans que cela soit signalé à l’appelant. Consultez les [métriques du portail](monitor-accounts.md).
* Azure Cosmos DB répartit le débit global approvisionné de manière uniforme entre les partitions physiques. Vérifiez les métriques du portail pour voir si la charge de travail rencontre une [clé de partition](partition-data.md) chaude. Cela a pour effet que le débit consommé (unités de demande/s) apparaît inférieur aux unités de demande approvisionnées, alors que le débit consommé par une seule partition dépasse le débit approvisionné. 
* De plus, le Kit de développement logiciel (SDK) 2.0 ajoute une sémantique de canal aux connexions directes/TCP. Une connexion TCP est utilisée pour traiter plusieurs demandes simultanément. Cela peut entraîner deux problèmes dans certains cas :
    * Un degré élevé de simultanéité peut entraîner des conflits sur le canal.
    * Des demandes ou réponses volumineuses peuvent entraîner un blocage en tête de ligne sur le canal et exacerber les conflits, même avec un degré relativement faible de simultanéité.
    * Si le cas s’inscrit dans l’une de ces deux catégories (ou en cas de soupçon d’utilisation élevée du processeur), les mesures d’atténuation possibles sont les suivantes :
        * Essayez d’adapter l’échelle de l’application.
        * Par ailleurs, vous pouvez capturer les journaux du Kit de développement logiciel (SDK) via l’[Écouteur de suivi](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) afin de recueillir davantage de détails.

### <a name="connection-throttling"></a>Limitation de la connexion
Une limitation de connexion peut se produire en raison d’une limite de connexion sur un ordinateur. Avant la version 2.0, les clients s’exécutant dans Azure pouvaient rencontrer un problème d’[Insuffisance de ports Azure SNAT (PAT)].

### <a name="snat"></a>Insuffisance de ports Azure SNAT (PAT)

Si votre application est déployée sur Machine virtuelle Azure sans adresse IP publique, par défaut les [ports Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) établissent des connexions avec n’importe quel point de terminaison en dehors de votre machine virtuelle. Le nombre de connexions autorisées de la machine virtuelle au point de terminaison Azure Cosmos DB est limité par la [configuration Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Les ports Azure SNAT sont utilisés uniquement quand votre machine virtuelle a une adresse IP privée et qu’un processus à partir de la machine virtuelle tente de se connecter avec une adresse IP publique. Il existe deux solutions de contournement pour éviter la limitation Azure SNAT :

* Ajoutez votre point de terminaison de service Azure Cosmos DB au sous-réseau de votre réseau virtuel Machines virtuelles Azure. Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quand le point de terminaison de service est activé, les requêtes ne sont plus envoyées d’une adresse IP publique à Azure Cosmos DB. Au lieu de cela, les identités du réseau virtuel et du sous-réseau sont envoyées. Cette modification peut entraîner des problèmes de pare-feu si seules les adresses IP publiques sont autorisées. Si vous utilisez un pare-feu, quand vous activez le point de terminaison de service, ajoutez un sous-réseau au pare-feu à l’aide de [Listes de contrôle d’accès de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Assignez une adresse IP publique à votre machine virtuelle Azure.

### <a name="http-proxy"></a>Serveur proxy HTTP
Si vous utilisez un proxy HTTP, vérifiez qu’il peut prendre en charge le nombre de connexions configuré dans SDK `ConnectionPolicy`.
Sinon, vous serez confronté à des problèmes de connexion.

### Taux de demandes trop élevé<a name="request-rate-too-large"></a>
Le message « Taux de demandes trop élevé » ou le code d’erreur 429 indiquent que vos demandes sont limitées parce que le débit consommé (unités de demande/s) a dépassé le débit approvisionné. Le Kit de développement logiciel (SDK) envoie automatiquement de nouvelles demandes en fonction de la [stratégie de nouvelles tentatives](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet) en place. Si cet échec se produit souvent, envisagez d’augmenter le débit de la collection. Examinez les [métriques du portail](use-metrics.md) pour voir si vous obtenez des erreurs 429. Examinez votre [clé de partition](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) pour vous assurer qu’elle entraîne une répartition uniforme du volume de stockage et de demande. 

### <a name="slow-query-performance"></a>Performances des requêtes lentes
Les [métriques de requête](sql-api-query-metrics.md) vous aident à déterminer où la requête passe la majeure partie du temps. Elle vous permettent de voir quelle portion du temps est consacrée au serveur principal et au client.
* Si la requête principale retourne rapidement des résultats, et passe beaucoup de temps sur le client, vérifiez la charge de la machine. Il est probable que les ressources sont insuffisantes et que le Kit de développement logiciel (SDK) attend que des ressources soient disponibles pour gérer la réponse.
* Si la requête principale est lente, essayez de l’[optimiser](optimize-cost-queries.md) et d’examiner la [stratégie d’indexation](index-overview.md) actuelle. 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taux de demandes trop élevé]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Insuffisance de ports Azure SNAT (PAT)]: #snat
[Production check list]: #production-check-list


