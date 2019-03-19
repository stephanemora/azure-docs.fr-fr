---
title: Diagnostiquer et résoudre les problèmes lors de l’utilisation d’Azure Cosmos DB .NET SDK
description: Fonctionnalités de l’utilisation, telles que la journalisation côté client et autres outils tiers pour identifier, diagnostiquer et résoudre les problèmes d’Azure Cosmos DB lors de l’utilisation du SDK .NET.
author: j82w
ms.service: cosmos-db
ms.date: 01/19/2019
ms.author: jawilley
ms.devlang: c#
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7f969ab6059140ec32c9c5bf5045c546602a3c15
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411351"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostiquer et résoudre les problèmes lors de l’utilisation d’Azure Cosmos DB .NET SDK
Cet article traite des problèmes courants, les solutions de contournement, les étapes de diagnostic et les outils lorsque vous utilisez le [.NET SDK](sql-api-sdk-dotnet.md) avec des comptes de l’API SQL Azure Cosmos DB.
Le kit SDK .NET fournit la représentation logique côté client pour accéder à l’API SQL Azure Cosmos DB. Cet article décrit les outils et les approches qui peuvent vous aider si vous rencontrez des problèmes.

## <a name="checklist-for-troubleshooting-issues"></a>Liste de vérification pour la résolution des problèmes :
Étudiez la liste suivante avant de passer votre application en production. L’utilisation de la liste de vérification va empêcher plusieurs problèmes courants que vous pouvez voir. Vous pouvez également rapidement diagnostiquer lorsqu’un problème se produit :

*   Utiliser la dernière version [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). Kits de développement logiciel en version préliminaire ne doivent pas être utilisées pour la production. Cela empêchera à rencontrer des problèmes connus déjà corrigés.
*   Consultez les [conseils relatifs aux performances](performance-tips.md) et suivez les pratiques suggérées. Cela permettra d’éviter la mise à l’échelle, la latence et autres problèmes de performances.
*   Activer la journalisation de kit de développement logiciel pour vous aider à résoudre un problème. L’activation de la journalisation peut affecter les performances il est donc préférable d’activer uniquement lorsque la résolution des problèmes. Vous pouvez activer les journaux suivants :
    *   [Journaliser les métriques](monitor-accounts.md) à l’aide du portail Azure. Mesures du portail affichent les données de télémétrie Azure Cosmos DB, ce qui est utile pour déterminer si le problème correspond à Azure Cosmos DB ou si elle est à partir du client.
    *   Journal du [chaîne diagnostics](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) des réponses d’opération de point.
    *   Journal du [métriques des requêtes SQL](sql-api-query-metrics.md) à partir de toutes les réponses de la requête 
    *   Suivez le programme d’installation pour [journalisation du Kit de développement logiciel]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Jetez un coup d’œil à la section [Problèmes courants et solutions de contournement](#common-issues-workarounds) dans cet article.

Vérifier le [GitHub émet section](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) qui est surveillé activement. Vérifiez si un problème similaire au vôtre dispose déjà d’une solution de contournement. Si vous ne trouvez pas une solution, puis signaler un incident GitHub. Vous pouvez ouvrir un cycle de prise en charge des problèmes urgents.


## <a name="common-issues-workarounds"></a>Problèmes courants et solutions de contournement

### <a name="general-suggestions"></a>Recommandations d’ordre général
* Exécutez votre application dans la même région Azure que votre compte Azure Cosmos DB, autant que possible. 
* Vous pouvez rencontrer des problèmes de connectivité/disponibilité en raison d’un manque de ressources sur votre ordinateur client. Nous recommandons de surveiller l’utilisation du processeur sur les nœuds qui exécutent le client Azure Cosmos DB, et la mise à l’échelle verticale ou horizontale si elles s’exécutent à charge élevée.

### <a name="check-the-portal-metrics"></a>Vérifier les métriques du portail
Vérification de la [mesures du portail](monitor-accounts.md) aidera à déterminer s’il s’agit d’un problème côté client ou s’il existe un problème avec le service. Par exemple si les métriques contiennent un taux élevé de demandes de limitation du taux (code d’état HTTP 429) ce qui signifie que la demande est sujet à des limitations puis vérifiez le [taux de demande trop grande] section. 

### <a name="request-timeouts"></a>Délais d’attente de demandes
RequestTimeout se produit généralement lors de l’utilisation de Direct/TCP, mais peut se produire en mode passerelle. Voici les causes connues courantes et des suggestions sur la façon de résoudre le problème.

* L’utilisation du processeur est élevé, qui sera entraîner une latence et/ou demander des délais d’attente. Le client peut augmenter l’ordinateur hôte afin de lui donner plus de ressources, ou la charge peut être distribuée sur plusieurs ordinateurs.
* Socket / de la disponibilité du Port peut être faible. Lorsque vous utilisez les kits de développement logiciel .NET publié avant la version 2.0, les clients s’exécutant dans Azure peuvent atteindre le [Insuffisance de ports Azure SNAT (PAT)]. Cet exemple de la raison pour laquelle il est recommandé de toujours exécuter la dernière version du Kit de développement logiciel.
* Création de plusieurs instances de DocumentClient peut entraîner des conflits de connexion et des problèmes de délai d’attente. Suivez le [conseils relatifs aux performances](performance-tips.md)et utiliser une seule instance de DocumentClient sur l’ensemble du processus.
* Les utilisateurs voient parfois des délais de latence ou de la demande avec élévation de privilèges, car leurs collections sont insuffisamment approvisionnées, le back-end limite les demandes, et le client effectue une nouvelle tentative en interne sans cela en exposant à l’appelant. Vérifier le [mesures du portail](monitor-accounts.md).
* Azure Cosmos DB distribue uniformément le débit approvisionné global entre plusieurs partitions physiques. Vérifier les mesures du portail pour voir si la charge de travail rencontre une chaud [clé de partition](partition-data.md). Cela entraîne le débit consommé (RU/s) à être s’affichera sous les RU approvisionnées, mais un partition unique consommé le débit (RU/s) dépasse le débit approvisionné. 
* En outre, le SDK 2.0 ajoute la sémantique de canal à des connexions direct/TCP. Une connexion TCP est utilisée pour plusieurs requêtes en même temps. Cela peut entraîner des deux problèmes sous des cas spécifiques :
    * Contention peut entraîner un haut degré de concurrence sur le canal.
    * Demandes volumineuses ou les réponses peuvent conduire au blocage de tête de la ligne sur le canal et aggraver la contention, même avec un nombre relativement faible degré de concurrence.
    * Si le cas se situe dans un de ces deux catégories (ou si l’utilisation élevée du processeur est suspecté), il s’agit des solutions possibles :
        * Essayez de mettre à l’échelle de l’application verticale ou horizontale.
        * En outre, les journaux du Kit de développement logiciel peuvent être capturés via [écouteur de suivi](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) pour obtenir plus de détails.

### <a name="connection-throttling"></a>Limitation de la connexion
Limitation de la connexion peut se produire en raison d’une limite de connexion sur un ordinateur hôte. Antérieures à 2.0, les clients s’exécutant dans Azure pourrait rencontrer le [Insuffisance de ports Azure SNAT (PAT)].

### <a name="snat"></a>Insuffisance de ports Azure SNAT (PAT)

Si votre application est déployée sur Machine virtuelle Azure sans adresse IP publique, par défaut les [ports Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) établissent des connexions avec n’importe quel point de terminaison en dehors de votre machine virtuelle. Le nombre de connexions autorisées de la machine virtuelle au point de terminaison Azure Cosmos DB est limité par la [configuration Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Les ports Azure SNAT sont utilisés uniquement quand votre machine virtuelle a une adresse IP privée et qu’un processus à partir de la machine virtuelle tente de se connecter avec une adresse IP publique. Il existe deux solutions de contournement pour éviter la limitation Azure SNAT :

* Ajoutez votre point de terminaison de service Azure Cosmos DB au sous-réseau de votre réseau virtuel Machines virtuelles Azure. Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quand le point de terminaison de service est activé, les requêtes ne sont plus envoyées d’une adresse IP publique à Azure Cosmos DB. Au lieu de cela, les identités du réseau virtuel et du sous-réseau sont envoyées. Cette modification peut entraîner des problèmes de pare-feu si seules les adresses IP publiques sont autorisées. Si vous utilisez un pare-feu, quand vous activez le point de terminaison de service, ajoutez un sous-réseau au pare-feu à l’aide de [Listes de contrôle d’accès de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Assignez une adresse IP publique à votre machine virtuelle Azure.

### <a name="http-proxy"></a>Serveur proxy HTTP
Si vous utilisez un proxy HTTP, vérifiez qu’il peut prendre en charge le nombre de connexions configuré dans SDK `ConnectionPolicy`.
Sinon, vous serez confronté à des problèmes de connexion.

### Taux de demande trop grande<a name="request-rate-too-large"></a>
Demande de taux trop grande ou code d’erreur 429 indique que vos requêtes sont limitées, car le débit consommé (RU/s) a dépassé le débit approvisionné. Le SDK retente automatiquement les demandes en fonction du [stratégie de nouvelle tentative](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Si vous obtenez souvent cet échec, envisagez d’augmenter le débit sur la collection. Vérifier le [les métriques du portail](use-metrics.md) pour voir si vous obtenez des 429 erreurs. Examinez votre [clé de partition](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) afin de s’en trouvent une répartition uniforme du volume de stockage et de la demande. 

### <a name="slow-query-performance"></a>Performances des requêtes lentes
Le [interroger métriques](sql-api-query-metrics.md) vous aideront à déterminer où la requête consacre la plupart du temps. Les métriques de requête, vous pouvez voir quelle partie de celui-ci est en cours passé sur les serveur principal vs le client.
* Si la requête principale renvoie rapidement et passe une durée importante sur le client, vérifiez la charge sur l’ordinateur. Il est probable qu’il n’existe pas suffisamment de ressources et le SDK est en attente de ressources soient disponibles pour gérer la réponse.
* Si la requête de serveur principal est lente essayez [optimisation de la requête](optimize-cost-queries.md) et en examinant actuel [stratégie d’indexation](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taux de demande trop grande]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Insuffisance de ports Azure SNAT (PAT)]: #snat
[Production check list]: #production-check-list


