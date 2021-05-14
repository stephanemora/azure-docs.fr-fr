---
title: Reprise d’activité pour Azure Purview
description: Découvrez comment configurer un environnement de récupération d’urgence pour Azure Purview.
author: sudheerreddykoppula
ms.author: sukoppul
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/23/2021
ms.openlocfilehash: 170f72c0076f6614e59c37ba0f06071480726b09
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108021139"
---
# <a name="disaster-recovery-for-purview"></a>Récupération d’urgence pour Purview

Cet article explique comment configurer un environnement de récupération d’urgence pour Azure Purview. Les pannes de centres de données Azure sont rares, mais elles peuvent durer de quelques minutes à plusieurs heures. Les pannes de centre de données peuvent entraîner une interruption des environnements sur lesquels la gouvernance des données repose. En suivant les étapes détaillées de cet article, vous pouvez continuer à gérer vos données en cas de panne du centre de données pour la région principale de votre compte Purview.

## <a name="achieve-business-continuity-for-azure-purview"></a>Assurer la continuité de l’activité pour Azure Purview

La continuité d’activité et reprise d’activité (BCDR) dans une instance Azure Purview se réfère aux mécanismes, stratégies et procédures qui permettent à votre entreprise de protéger les données perdues et de continuer à fonctionner en cas de perturbation, en particulier pour ses niveaux d’analyse, de catalogue et d’insights. Cette page explique comment configurer un environnement de récupération d’urgence pour Azure Purview.

Aujourd’hui, Azure Purview ne prend pas en charge la BCDR automatisée. Jusqu’à ce que la prise en charge soit ajoutée, vous devez assurer les activités de sauvegarde et de restauration. Vous pouvez créer manuellement un compte Purview secondaire comme instance de secours semi-automatique dans une autre région.

Les étapes suivantes montrent comment vous pouvez effectuer la récupération d’urgence manuellement :

1. Une fois le compte Purview principal créé dans une certaine région, vous devez approvisionner un ou plusieurs comptes Purview secondaires dans des régions distinctes à partir du portail Azure. 

2. Toutes les activités effectuées sur le compte Purview principal doivent également être effectuées sur les comptes Purview secondaires. notamment : 

    - Tenir à jour les informations de compte
    - Créer et gérer des ensembles de règles d’analyse personnalisée, des classifications et des règles de classification
    - Inscrire et analyser les sources
    - Créer et gérer des collections avec l’association de sources aux collections
    - Créer et gérer les informations d’identification utilisées lors de l’analyse.
    - Organiser les ressources de données
    - Créer et tenir à jour les termes du glossaire


Lorsque vous planifiez votre plan BCDR manuel, gardez les points suivants à l’esprit : 

- Vous serez facturé pour les comptes Purview principaux et secondaires. 

- Les comptes Purview principaux et secondaires ne peuvent pas être configurés pour les mêmes comptes Azure Data Factory, Azure Data Share et Synapse Analytics, le cas échéant.  Par conséquent, la traçabilité d’Azure Data Factory et d’Azure Data Share ne peut pas être vue dans les comptes Purview secondaires. En outre, l’espace de travail Synapse Analytics associé au compte Purview principal ne peut pas être associé à des comptes Purview secondaires. Il s’agit d’une limitation aujourd’hui, qui sera traitée quand la BCDR automatisée sera prise en charge. 

- Les runtimes d’intégration sont spécifiques à un compte Purview. Par conséquent, si les analyses doivent s’exécuter dans des comptes Purview principaux et secondaires en parallèle, plusieurs runtimes d’intégration auto-hébergés doivent être maintenus. Cette limitation sera également traitée quand la BCDR automatisée sera prise en charge. 

- L’exécution parallèle d’analyses à partir de comptes Purview principaux et secondaires sur la même source peut affecter les performances de la source. Cela peut entraîner des variations dans les durées d’analyse entre les comptes Purview.   

## <a name="related-information"></a>Informations connexes

- [Continuité d’activité et reprise d’activité](../best-practices-availability-paired-regions.md)
- [Créer une haute disponibilité dans une stratégie BCDR](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)
- [Statut Azure](https://status.azure.com/status)

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec Azure Purview, consultez [Créer un compte Azure Purview](create-catalog-portal.md).
