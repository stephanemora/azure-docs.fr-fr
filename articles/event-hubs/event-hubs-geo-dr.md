---
title: Géorécupération d’urgence - Azure Event Hubs | Microsoft Docs
description: Découvrez comment utiliser les régions géographiques pour le basculement et la récupération d’urgence dans Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 61318fbccdf92c6502aa8b2236d8b234cec67668
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209143"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs - Géorécupération d’urgence 

Si tout un centre de données ou une région Azure complète (si aucune [zone de disponibilité](../availability-zones/az-overview.md) n’est utilisée) connaît un temps d’arrêt, il est essentiel que le traitement des données puisse continuer dans les autres régions ou centres de données. Pour cette raison, la *géorécupération d’urgence* et la *géoréplication* sont des fonctionnalités importantes pour les entreprises. Azure Event Hubs prend en charge la géorécupération d’urgence et la géoréplication au niveau de l’espace de noms. 

> [!NOTE]
> La fonctionnalité de géorécupération d’urgence est disponible uniquement pour les [références SKU standard et dédiées](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Pannes et sinistres

Il est important de noter la différence entre « panne » et « sinistre ». Une *panne* se définit comme l’indisponibilité temporaire d’Azure Event Hubs. La panne impacte certains composants du service, comme une banque de messages, ou le centre de données entier. Toutefois, une fois le problème résolu, Event Hubs redevient disponible. En règle générale, une panne ne provoque aucune perte de messages ou d’autres données. Une coupure de courant dans le centre de données est un exemple de panne. Certaines pannes sont uniquement dues à une courte perte de la connexion en raison de problèmes réseau ou de soucis temporaires. 

Un *sinistre* se définit comme une perte définitive ou à long terme d’un cluster Event Hubs, d’une région Azure ou d’un centre de données. La région ou le centre de données peut redevenir disponible ou pas, et rester arrêté(e) pendant plusieurs heures ou jours. Les incendies, les inondations ou les tremblements de terre sont des exemples de sinistres. Un sinistre qui devient permanent peut entraîner la perte de certains messages, événements ou d’autres données. Toutefois, dans la plupart des cas, il ne doit y avoir aucune perte de données et les messages peuvent être récupérés une fois que le centre de données est sauvegardé.

La fonctionnalité de géorécupération d’urgence d’Azure Event Hubs est une solution de récupération d’urgence. Les concepts et le workflow décrits dans cet article concernent des scénarios de sinistres, pas des pannes temporaires ou transitoires. Pour obtenir une présentation détaillée de la récupération d’urgence dans Microsoft Azure, consultez [cet article](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Concepts et terminologie de base

La fonctionnalité de récupération d’urgence implémente la récupération d’urgence des métadonnées, en s’appuyant sur les espaces de noms de récupération d’urgence principal et secondaire. 

La fonctionnalité de géorécupération d’urgence est disponible uniquement pour les [références SKU standard et dédiées](https://azure.microsoft.com/pricing/details/event-hubs/). Vous n’avez pas besoin de modifier la chaîne de connexion, car la connexion est établie à l’aide d’un alias.

Cet article emploie les termes suivants :

-  *Alias* : nom d’une configuration de récupération d’urgence que vous avez configurée. L’alias fournit une chaîne de connexion de nom de domaine complet (FQDN) stable. Les applications utilisent cet alias de chaîne de connexion pour se connecter à un espace de noms. 

-  *Espace de noms principal/secondaire* : espaces de noms qui correspondent à l’alias. L’espace de noms principal est « actif » et reçoit les messages (il peut s’agir d’un espace de noms existant ou nouveau). L’espace de noms secondaire est « passif » et ne reçoit pas de messages. Les métadonnées sont synchronisées entre ces deux espaces de noms, qui peuvent ainsi accepter facilement les messages sans aucune modification du code d’application ou de la chaîne de connexion. Pour vous assurer que seul l’espace de noms actif reçoit des messages, vous devez utiliser l’alias. 

-  *Métadonnées* : entités telles que des concentrateurs d’événements et des groupes de consommateurs ; incluent également leurs propriétés sur le service associé à l’espace de noms. Notez que seules les entités et leurs paramètres sont automatiquement répliqués. Les messages et les événements ne sont pas répliqués. 

-  *Basculement* : processus d’activation de l’espace de noms secondaire.

## <a name="supported-namespace-pairs"></a>Paires d’espaces de noms prises en charge
Les combinaisons suivantes d’espaces de noms principaux et secondaires sont prises en charge :  

| Espace de noms principal | Espace de noms secondaire | Prise en charge | 
| ----------------- | -------------------- | ---------- |
| standard | standard | Oui | 
| standard | Dédié | Oui | 
| Dédié | Dédié | Oui | 
| Dédié | standard | Non | 

> [!NOTE]
> Vous ne pouvez pas associer des espaces de noms qui se trouvent dans le même cluster dédié. Vous pouvez associer des espaces de noms qui se trouvent dans des cluster distincts. 

## <a name="setup-and-failover-flow"></a>Flux de configuration et de basculement

La section suivante présente une vue d’ensemble du processus de basculement et explique comment configurer le basculement initial. 

![1][]

### <a name="setup"></a>Programme d’installation

Tout d’abord, vous créez ou utilisez un espace de noms principal existant et un espace de noms secondaire, avant d’associer les deux. Cette association crée un alias qui vous servira à vous connecter. Étant donné que vous utilisez un alias, vous n’avez pas besoin de modifier les chaînes de connexion existantes. Vous pouvez uniquement ajouter de nouveaux espaces de noms à votre association de basculement. Enfin, vous devez ajouter un système de surveillance afin de détecter si un basculement est nécessaire. Dans la plupart des cas, le service fait partie d’un écosystème de grande taille. C’est pourquoi les basculements automatiques sont rarement possibles, dans la mesure où, très souvent, les basculements doivent être synchronisés avec le reste de l’infrastructure ou du sous-système.

### <a name="example"></a>Exemple

Dans un exemple de ce scénario, imaginez une solution de Point de vente (PDV) qui émet des messages ou des événements. Event Hubs transmet ces événements à une solution de mappage ou de reformatage, qui envoie ensuite les données mappées à un autre système pour traitement. À ce stade, tous ces systèmes peuvent être hébergés dans la même région Azure. Le choix du moment du basculement et des éléments à basculer varie selon le flux de données dans votre infrastructure. 

Vous pouvez automatiser le basculement à l’aide de systèmes de surveillance ou à l’aide de solutions de surveillance personnalisées. Toutefois, cette automatisation nécessite des tâches de planification et du travail supplémentaires, qui ne seront pas abordés dans cet article.

### <a name="failover-flow"></a>Flux de basculement

Si vous lancez le basculement, deux étapes sont requises :

1. Vous voulez être sûr de pouvoir refaire un basculement en cas de nouvelle panne. Pour cela, configurez un autre espace de noms passif et mettez à jour l’association. 

2. Tirez (pull) les messages à partir de l’ancien espace de noms principal une fois qu’il est de nouveau disponible. Après cela, utilisez cet espace de noms pour les messages réguliers en dehors de votre configuration de géorécupération ou supprimez l’ancien espace de noms principal.

> [!NOTE]
> Seule la sémantique de transfert du basculement est prise en charge. Dans ce scénario, vous basculez puis effectuez un nouveau couplage avec un nouvel espace de noms. La restauration automatique n’est pas prise en charge ; par exemple, dans un cluster SQL. 

![2][]

## <a name="management"></a>Gestion

Si vous avez fait une erreur (par exemple, vous avez associé les mauvaises régions lors de la configuration initiale), vous pouvez rompre le couplage des deux espaces de noms à tout moment. Si vous souhaitez utiliser les espaces de noms couplés comme des espaces de noms standard, supprimez l’alias.

## <a name="samples"></a>Exemples

L’[exemple sur GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) montre comment configurer et lancer un basculement. Cet exemple illustre les concepts suivants :

- Paramètres requis dans Azure Active Directory pour utiliser Azure Resource Manager avec Event Hubs. 
- Étapes requises pour exécuter l’exemple de code. 
- Envoi et réception à partir de l’espace de noms principal actuel. 

## <a name="considerations"></a>Considérations

Notez les points suivants pour cette version :

1. De par sa conception, la géo-reprise d'activité après sinistre Event Hubs ne réplique pas les données, et vous ne pouvez donc pas réutiliser l'ancienne valeur de décalage de votre hub d'événements principal sur votre hub d'événements secondaire. Nous vous recommandons de redémarrer votre récepteur d'événements avec l'un des éléments suivants :

- *EventPosition.FromStart()*  : pour lire toutes les données sur votre hub d'événements secondaire.
- *EventPosition.FromEnd()*  : pour lire toutes les nouvelles données à partir de la connexion à votre hub d'événements secondaire.
- *EventPosition.FromEnqueuedTime(dateTime)*  : pour lire toutes les données reçues dans votre hub d'événements secondaire depuis une date et une heure précises.

2. Dans votre planification de basculement, vous devez également tenir compte du facteur temps. Par exemple, si vous perdez la connectivité pendant plus de 15 à 20 minutes, vous pouvez décider de lancer le basculement. 
 
3. Le fait qu’aucune donnée ne soit répliquée signifie que les sessions actuellement actives ne sont pas répliquées. En outre, la détection des doublons et les messages planifiés peuvent ne pas fonctionner. Les nouvelles sessions, les messages planifiés et les nouveaux doublons fonctionneront. 

4. Le basculement d’une infrastructure distribuée complexe doit être [répétée](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) au moins une fois. 

5. La synchronisation des entités peut prendre un certain temps, à raison d’environ 50 à 100 entités par minute.

## <a name="availability-zones"></a>Zones de disponibilité 

La référence SKU Event Hubs Standard prend en charge les [zones de disponibilité](../availability-zones/az-overview.md), fournissant ainsi des emplacements isolés des défaillances au sein d'une région Azure. 

> [!NOTE]
> Pour Azure Event Hubs Standard, la prise en charge des zones de disponibilité s'applique uniquement aux [régions Azure](../availability-zones/az-region.md) où des zones de disponibilité sont déjà présentes.

Vous pouvez activer les Zones de disponibilité sur les nouveaux espaces de noms uniquement, à l’aide du portail Azure. Event Hubs ne prend pas en charge la migration des espaces de noms existants. Vous ne pouvez pas désactiver la redondance de zone après l’avoir activée sur votre espace de noms.

![3][]

## <a name="next-steps"></a>Étapes suivantes

* [L’exemple sur GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) décrit un flux de travail simple qui crée un géocouplage et déclenche un basculement pour un scénario de récupération d’urgence.
* La [référence d’API REST](/rest/api/eventhub/disasterrecoveryconfigs) décrit les API nécessaires pour effectuer la configuration de la géorécupération.

Pour plus d’informations sur les concentrateurs d’événements, accédez aux liens suivants :

- Prise en main des hubs d’événements
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [FAQ sur les hubs d’événements](event-hubs-faq.md)
* [Exemples d’application complets qui utilisent des concentrateurs d’événements](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
