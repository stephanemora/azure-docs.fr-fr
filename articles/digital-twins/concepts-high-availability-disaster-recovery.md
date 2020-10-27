---
title: Haute disponibilité et récupération d’urgence
titleSuffix: Azure Digital Twins
description: Décrit les options Azure et Azure Digital Twins qui vous aident à créer des solutions Azure IoT à haute disponibilité dotées de fonctionnalités de récupération d’urgence.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 85fd5a4246e891ef6640438b07e12a9c32ad12fa
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094126"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Haute disponibilité et récupération d’urgence pour Azure Digital Twins

La continuité d'activité et reprise d'activité constitue un domaine clé à prendre en compte pour des solutions IoT résilientes. Une conception pensée pour la **haute disponibilité (HA)** et la **récupération d’urgence (DR)** peut vous aider à définir et atteindre les objectifs de temps de disponibilité appropriés pour votre solution.

Cet article décrit les fonctionnalités de haute disponibilité et de récupération d’urgence offertes par le service Azure Digital Twins.

Azure Digital Twins prend en charge les options de fonctionnalité suivantes :
* *HA intra-région*  : redondance intégrée pour assurer le temps de disponibilité du service
* *DR inter-régions* : basculement vers une région Azure associée géographiquement dans le cas d’une défaillance inattendue du centre de données

Vous pouvez également consulter la section [*Meilleures pratiques*](#best-practices) pour obtenir des conseils généraux sur la conception de haute disponibilité/récupération d’urgence.

## <a name="intra-region-ha"></a>Haute disponibilité intra-région
 
Azure Digital Twins fournit la haute disponibilité intra-région en implémentant des redondances au sein du service. **Les développeurs d’une solution Azure Digital Twins peuvent tirer parti de ces fonctionnalités de haute disponibilité sans le moindre effort supplémentaire.** Bien qu’Azure Digital Twins offre une garantie de disponibilité raisonnablement élevée, des défaillances temporaires peuvent toujours se produire comme avec n’importe quelle plateforme informatique distribuée. Des stratégies de nouvelle tentative appropriées doivent être intégrées dans les composants qui interagissent avec une application cloud de manière à gérer les défaillances temporaires.

## <a name="cross-region-dr"></a>Récupération d’urgence inter-région

Il peut arriver à de rares occasions qu’un centre de données connaisse une interruption prolongée en raison de pannes d’alimentation ou d’autres événements dans la région. Ces événements se produisent rarement et, lors de tels problèmes, la fonction de haute disponibilité intra-région décrite ci-dessus ne peut pas nécessairement s’appliquer dans ces situations. Azure Digital Twins répond à cela avec le basculement initié par Microsoft.

Le **basculement initié par Microsoft** est déclenché par Microsoft dans de rares situations pour faire basculer tous les Azure Digital Twins entre une région affectée et la région géographiquement associée. Ce processus représente l’option par défaut (sans aucun moyen pour les utilisateurs d’y renoncer) et ne nécessite aucune intervention de la part de l’utilisateur. Microsoft se réserve le droit de déterminer dans quelles circonstances exercer cette option. Ce mécanisme n’implique pas le consentement de l’utilisateur avant le basculement de l’instance de l’utilisateur.

>[!NOTE]
> Certains services Azure fournissent également une option supplémentaire appelée **basculement initié par le client** , qui permet aux clients de lancer un basculement uniquement pour leur instance, par exemple pour exécuter un exercice de récupération d’urgence. Ce mécanisme n’est actuellement **pas pris en charge** par Azure Digital Twins. 

## <a name="best-practices"></a>Meilleures pratiques

Pour connaître les meilleures pratiques en matière de haute disponibilité/récupération d’urgence, consultez les conseils Azure suivants sur cette rubrique : 
* L’article [*Conception d’applications résilientes pour Azure*](/azure/architecture/framework/resiliency/overview) décrit un cadre général qui vous aide à réfléchir aux questions de continuité d’activité et de reprise d’activité. 
* Le document [*Récupération d’urgence et haute disponibilité pour les applications Azure*](/azure/architecture/framework/resiliency/backup-and-recovery) contient des recommandations d’architecture concernant les stratégies permettant de mettre en place la haute disponibilité et la récupération d’urgence dans les applications Azure.

## <a name="next-steps"></a>Étapes suivantes 

En savoir plus sur la prise en main des solutions Azure Digital Twins :
 
* [*Qu’est-ce qu’Azure Digital Twins ?*](overview.md)
* [*Démarrage rapide : Explorer un exemple de scénario*](quickstart-adt-explorer.md)