---
title: Disponibilité et continuité
titleSuffix: Azure Cognitive Search
description: Apprenez à rendre un service de recherche hautement disponible et résilient face aux perturbations périodiques, voire aux défaillances catastrophiques.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 493f6759f63f023572f38647076e04425acf9d6a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581525"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Disponibilité et continuité opérationnelle dans Recherche cognitive Azure

Dans Recherche cognitive, la disponibilité est assurée par plusieurs réplicas, tandis que la continuité opérationnelle (et la récupération d’urgence) est assurée par plusieurs services de recherche. Cet article fournit des conseils que vous pouvez utiliser comme point de départ pour élaborer une stratégie qui répond aux exigences de votre entreprise en matière de disponibilité et de continuité opérationnelle.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>Haute disponibilité

Dans Recherche cognitive, les réplicas sont des copies de votre index. L’utilisation de plusieurs réplicas permet au service Recherche cognitive Azure de gérer les redémarrages et la maintenance des machines pour un réplica spécifique, alors que les requêtes continuent de s’exécuter sur d’autres réplicas. Pour plus d’informations sur l’ajout de réplicas, consultez [Ajouter ou enlever des réplicas et des partitions](search-capacity-planning.md#adjust-capacity).

Pour chaque service de recherche, Microsoft garantit une disponibilité d’au moins 99,9 % pour les configurations qui répondent à ces critères : 

+ Deux réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)

+ Trois réplicas (ou plus) pour la haute disponibilité des charges de travail en lecture-écriture (requêtes et indexation) 

Aucun Contrat de niveau de service n’est fourni pour le niveau Gratuit. Pour plus d’informations, consultez [SLA pour Recherche cognitive Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<a name="availability-zones"></a>

## <a name="availability-zones"></a>Zones de disponibilité

[Zones de disponibilité](../availability-zones/az-overview.md) est une capacité de la plateforme Azure qui divise les centres de données d’une région en groupes situés dans des emplacements physiques distincts afin de fournir une haute disponibilité au sein d’une même région. Si vous utilisez Zones de disponibilité pour Recherche cognitive, les réplicas correspondent aux unités de l’attribution de zone. Le service de recherche s’exécute dans une région, et ses réplicas dans différentes zones.

Vous pouvez utiliser des zones de disponibilité avec le service Recherche cognitive Azure en ajoutant au moins deux réplicas à votre service de recherche. Chaque réplica sera placé dans une zone de disponibilité distincte au sein de la région. Si vous avez plus de réplicas qu’il n’y a de zones de disponibilité, les réplicas sont répartis entre les zones de disponibilité aussi uniformément que possible. Vous n’avez aucune action spécifique à effectuer, si ce n’est de [créer un service de recherche](search-create-service-portal.md) dans une région qui fournit Zones de disponibilité, puis de configurer le service pour qu’il [utilise plusieurs réplicas](search-capacity-planning.md#adjust-capacity).

Le service Recherche cognitive Azure prend actuellement en charge les zones de disponibilité pour les services de recherche de niveau standard ou supérieurs qui ont été créés dans l’une des régions suivantes :

+ Australie Est (créée le 30 janvier 2021 ou ultérieurement)
+ Canada Centre (créée le 30 janvier 2021 ou ultérieurement)
+ USA Centre (créée le 4 décembre 2020 ou ultérieurement)
+ USA Est (créée le 27 janvier 2021 ou ultérieurement)
+ USA Est 2 (créée le 30 janvier 2021 ou ultérieurement)
+ France Centre (créée le 23 octobre 2020 ou ultérieurement)
+ Japon Est (créée le 30 janvier 2021 ou ultérieurement)
+ Europe Nord (créée le 28 janvier 2021 ou ultérieurement)
+ Asie Sud-Est (créée le 31 janvier 2021 ou ultérieurement)
+ Royaume-Uni Sud (créée le 30 janvier 2021 ou ultérieurement)
+ Europe Ouest (créée le 29 janvier 2021 ou ultérieurement)
+ USA Ouest 2 (créée le 30 janvier 2021 ou ultérieurement)

Les zones de disponibilité n’ont pas d’incidence sur le [Contrat de niveau de service de la Recherche cognitive Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Vous avez toujours besoin d’au moins trois réplicas pour la haute disponibilité des requêtes.

## <a name="multiple-services-in-separate-geographic-regions"></a>Plusieurs services dans des régions géographiques distinctes

Bien que la plupart des clients utilisent un seul service, une redondance des services peut être nécessaire en cas d’exigences opérationnelles particulières, notamment :

+ [Continuité d’activité et reprise d’activité (BCDR)](../best-practices-availability-paired-regions.md) [Recherche cognitive ne fournit pas de basculement instantané en cas de panne.]
+ Applications déployées à l’échelle mondiale. Si les demandes d’interrogation et d’indexation proviennent du monde entier, les utilisateurs les plus proches du centre de données hôte bénéficieront de performances plus rapides. La création de services supplémentaires dans les régions les plus proches de ces utilisateurs peut égaliser les performances pour tous les utilisateurs.
+ Les [architectures mutualisées](search-modeling-multitenant-saas-applications.md) appellent parfois deux ou plusieurs services.

Si vous avez besoin de deux services de recherche supplémentaires, leur création dans des régions différentes peut répondre aux exigences des applications en matière de continuité et de récupération, ainsi qu’à des temps de réponse plus rapides pour une base d’utilisateurs mondiale.

Recherche cognitive Azure n’offre pas actuellement de méthode automatisée de géoréplication des index de recherche entre les régions, mais quelques techniques permettent d’implémenter et de gérer simplement ce processus. Elles sont présentées dans les sections suivantes.

L’objectif d’un ensemble géodistribué de services de recherche est de disposer de plusieurs index dans au moins deux régions et de rediriger l’utilisateur vers le service Recherche cognitive Azure qui offre la plus faible latence :

   ![Tableau croisé des services par région][1]

Vous pouvez implémenter cette architecture en créant plusieurs services et en concevant une stratégie de synchronisation des données. Si vous le souhaitez, vous pouvez inclure une ressource comme Azure Traffic Manager pour le routage des demandes. Pour plus d’informations, consultez [Créer un service de recherche](search-create-service-portal.md).

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>Garantir la synchronisation des données entre plusieurs services

Il existe deux méthodes pour synchroniser plusieurs services de recherche distribués : [l’indexeur de Recherche cognitive Azure](search-indexer-overview.md) ou l’API Push (également appelée [API REST Recherche cognitive Azure](/rest/api/searchservice/)). 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>Option 1 : Mettre à jour le contenu dans plusieurs services à l’aide d’indexeurs

Si vous utilisez déjà un indexeur sur un service unique, vous pouvez configurer un second indexeur sur un deuxième service pour qu’il utilise le même objet de source de données, en extrayant les données au même emplacement. Chacun des services de chaque région dispose de son propre indexeur et d’un index cible (votre index de recherche n’est pas partagé, ce qui signifie que les données sont dupliquées), mais chaque indexeur référence la même source de données.

Voici une vue d’ensemble de l’aspect d’une telle architecture.

   ![Source de données unique avec indexeur distribué et combinaisons de service][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Option 2 : Envoyer (push) les mises à jour de contenu à plusieurs services à l’aide d’API REST

Si vous utilisez l’API REST Recherche cognitive Azure pour [envoyer (push) le contenu à votre index de recherche](tutorial-optimize-indexing-push-api.md), vous pouvez assurer la synchronisation continue de vos différents services de recherche en envoyant les modifications à tous ces services chaque fois qu’une mise à jour est nécessaire. Dans votre code, veillez à gérer les cas dans lesquels la mise à jour d’un service de recherche échoue, mais réussit pour d’autres services de recherche.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Utiliser Azure Traffic Manager pour coordonner les demandes

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) vous permet d’acheminer les requêtes vers plusieurs sites géo-localisés et pris en charge par plusieurs services de recherche. Traffic Manager offre l’avantage de pouvoir tester la Recherche cognitive Azure pour vous assurer qu’il est disponible et de rediriger les utilisateurs vers d’autres services de recherche en cas d’interruption du service. En outre, si vous acheminez des requêtes de recherche via des sites Web Azure, Azure Traffic Manager permet d’équilibrer les charges lorsque le site web est opérationnel mais pas la Recherche cognitive Azure. Voici un exemple d’architecture tirant parti de Traffic Manager.

   ![Tableau croisé des services par région, avec Traffic Manager central][3]

## <a name="disaster-recovery-and-service-outages"></a>Récupération d’urgence et pannes de service

Bien que nous puissions récupérer vos données, Recherche cognitive Azure ne fournit pas de basculement instantané du service en cas de panne au niveau du centre de données ou du cluster. Si un cluster tombe en panne dans le centre de données, l’équipe d’exploitation le détecte et tente de restaurer le service. Vous rencontrerez un temps d’arrêt pendant la restauration de service, mais vous pouvez demander des crédits de service afin de compenser une indisponibilité du service conformément au [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Si le service ne doit pas être interrompu même en cas de défaillances catastrophiques qui échappent au contrôle de Microsoft, vous pouvez [approvisionner un service supplémentaire](search-create-service-portal.md) dans une autre région et mettre en œuvre une stratégie de géoréplication pour assurer une redondance complète des index sur tous les services.

Les clients qui utilisent des [indexeurs](search-indexer-overview.md) pour remplir et actualiser les index peuvent gérer la récupération d’urgence par le biais d’indexeurs propres à la région qui exploitent la même source de données. Deux services situés dans des régions différentes, chacun exécutant un indexeur, peuvent indexer la même source de données pour bénéficier de la géoredondance. Si vous indexez à partir de sources de données qui sont aussi géoredondantes, sachez que les indexeurs de Recherche cognitive Azure ne peuvent assurer qu’une indexation incrémentielle (en fusionnant les mises à jour de documents nouveaux, modifiés ou supprimés) à partir de réplicas principaux. À l’occasion d’un basculement, veillez à refaire pointer l’indexeur vers le nouveau réplica principal. 

Si vous n’utilisez pas d’indexeurs, vous devez utiliser le code de votre application pour effectuer une transmission de type push des objets et des données vers différents services de recherche en parallèle. Pour plus d’informations, consultez [Garantir la synchronisation des données entre plusieurs services](#data-sync).

## <a name="back-up-and-restore-alternatives"></a>Alternatives de sauvegarde et de restauration

Recherche cognitive Azure n’est pas une solution de stockage de données principal. C’est pourquoi Microsoft ne fournit pas de mécanisme formel de sauvegarde et de restauration en libre-service. Toutefois, vous pouvez utiliser l’exemple de code **index-backup-restore** dans cet [exemple de référentiel .NET Recherche cognitive Azure](https://github.com/Azure-Samples/azure-search-dotnet-samples) pour sauvegarder la définition et l’instantané d’un index dans une série de fichiers JSON, puis utiliser ces fichiers pour restaurer l’index, le cas échéant. Cet outil peut également déplacer des index entre les niveaux de service.

Sinon, le code de votre application utilisé pour créer et remplir un index est l’option de restauration de facto si vous supprimez un index par erreur. Pour reconstruire un index, vous devez le supprimer (s’il existe), recréer l’index dans le service et le recharger en récupérant les données à partir de votre banque de données principale.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les niveaux tarifaires et les limites de service de chacun d’eux, consultez [Limites de service](search-limits-quotas-capacity.md). Consultez la page [Planification des capacités](search-capacity-planning.md) pour en savoir plus sur les combinaisons de partitions et de réplicas.

Pour une discussion sur les performances et des démonstrations des techniques présentées dans cet article, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png