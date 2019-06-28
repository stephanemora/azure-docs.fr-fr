---
title: Administration des services pour Recherche Azure sur le portail - Recherche Azure
description: Gérez Recherche Azure, un service de recherche cloud hébergé sur Microsoft Azure, à l'aide du portail Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d5820c927b88eba37eaf092dfd4b209180bfc8eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565432"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administration des services pour Azure Search dans le portail Azure
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Search est un service de recherche entièrement géré, basé sur le cloud, utilisé pour la création d’une expérience de recherche riche dans des applications personnalisées. Cet article aborde les tâches d’administration des services que vous pouvez effectuer dans le [portail Azure](https://portal.azure.com) pour un service de recherche que vous avez déjà provisionné. L’administration des services est légère de par sa conception et se limite aux tâches suivantes :

> [!div class="checklist"]
> * Gérer l’accès aux *clés API* utilisées pour l’accès en lecture ou en écriture à votre service.
> * Ajustez la capacité de service en modifiant l’allocation des partitions et des réplicas.
> * Surveillez l’utilisation des ressources, par rapport aux limites maximales de votre niveau de service.

Notez que la *mise à niveau* n’est pas répertoriée comme une tâche administrative. Sachant que les ressources sont allouées pendant l’approvisionnement du service, le passage à un autre niveau nécessite de créer un nouveau service. Pour plus d’informations, consultez [Création d’un service Azure Search](search-create-service-portal.md).

> [!Tip]
> Besoin d’aide sur la façon d’analyser le trafic des recherches ou le niveau de performance des requêtes ? Vous pouvez surveiller le volume de requêtes, les termes recherchés par les utilisateurs et l’efficacité avec laquelle les résultats de la recherche guident les clients vers des documents spécifiques dans votre index. Pour plus d’informations, consultez [Analytique du trafic de recherche pour Recherche Azure](search-traffic-analytics.md), [Surveiller les métriques relatives à l’utilisation et aux requêtes](search-monitor-usage.md) et [Niveau de performance et optimisation](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Droits d’administrateur
L’approvisionnement ou le retrait du service lui-même peut être effectué par un administrateur d’abonnement Azure ou un coadministrateur.

Au sein d’un service, quiconque ayant accès à l’URL du service et disposant d’une clé API d’administration bénéficie d’un accès en lecture-écriture au service. L’accès en lecture-écriture permet d’ajouter, de supprimer ou de modifier des objets serveur, notamment des clés API, des index, des indexeurs, des sources de données, des planifications et des attributions de rôles, tels qu’implémentés via les [rôles définis par RBAC](search-security-rbac.md).

Toutes les interactions utilisateur avec Azure Search sont soumises à un de ces modes : accès en lecture-écriture au service (droits d’administrateur) ou un accès en lecture seule au service (droits de requête). Pour plus d’informations, consultez la page [Gestion des clés API](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Journalisation et informations système
Azure Search n’expose pas les fichiers journaux d’un service via le portail ou les interfaces de programmation. Au niveau de base et supérieur, Microsoft surveille tous les services Azure Search pour vérifier la disponibilité de 99,9 % par contrat de niveau de service (SLA). Si le service est lent ou si le débit des demandes tombe en dessous des seuils de contrat SLA, les équipes de support passent en revue les fichiers journaux à leur disposition et résolvent le problème.

En termes d’informations générales relatives à votre service, vous pouvez obtenir des informations de plusieurs façons :

* Sur le portail, le tableau de bord du service, via les notifications, les propriétés et les messages d’état.
* À l’aide de [PowerShell](search-manage-powershell.md) ou de [l’API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/) pour [obtenir les propriétés du service](https://docs.microsoft.com/rest/api/searchmanagement/services), ou de l’état sur l’utilisation des ressources d’index.
* Via la [recherche du trafic d’analyse](search-traffic-analytics.md), comme indiqué précédemment.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>surveiller l’utilisation des ressources ;
Sur le tableau de bord, l’analyse des ressources se limite aux informations affichées dans le tableau de bord des services et à quelques mesures que vous pouvez obtenir en interrogeant le service. Dans la section Utilisation du tableau de bord des services, vous pouvez déterminer rapidement si les niveaux des ressources de partition sont adaptés à votre application. Vous pouvez provisionner des ressources externes, telles que la supervision Azure, si vous souhaitez capturer et conserver les événements enregistrés. Pour plus d’informations, consultez [Supervision de la Recherche Azure](search-monitor-usage.md).

L’API REST Search Service vous permet d’obtenir le nombre de documents et d’index par programmation : 

* [Obtention de statistiques d’index](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Nombre de documents](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Récupération d’urgence et pannes de service

Bien que nous puissions récupérer vos données, la Recherche Azure ne fournit pas de basculement instantané du service en cas de panne au niveau du centre de données ou du cluster. Si un cluster tombe en panne dans le centre de données, l’équipe d’exploitation le détecte et tente de restaurer le service. Vous rencontrerez un temps d’arrêt pendant la restauration de service, mais vous pouvez demander des crédits de service afin de compenser une indisponibilité du service conformément au [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Si le service ne doit pas être interrompu même en cas de défaillances catastrophiques qui échappent au contrôle de Microsoft, vous pouvez [approvisionner un service supplémentaire](search-create-service-portal.md) dans une autre région et mettre en œuvre une stratégie de géoréplication pour assurer une redondance complète des index sur tous les services.

Les clients qui utilisent des [indexeurs](search-indexer-overview.md) pour remplir et actualiser les index peuvent gérer la récupération d’urgence par le biais d’indexeurs propres à la région qui exploitent la même source de données. Deux services situés dans des régions différentes, chacun exécutant un indexeur, peuvent indexer la même source de données pour bénéficier de la géoredondance. Si vous indexez à partir de sources de données qui sont aussi géo-redondantes, sachez que les indexeurs du service Recherche Azure ne peuvent assurer qu’une indexation incrémentielle à partir de réplicas principaux. À l’occasion d’un basculement, veillez à refaire pointer l’indexeur vers le nouveau réplica principal. 

Si vous n’utilisez pas d’indexeurs, vous devez utiliser le code de votre application pour effectuer une transmission de type push des objets et des données vers différents services de recherche en parallèle. Pour plus d’informations, consultez [Performance and optimization in Azure Search](search-performance-optimization.md)(Performances et optimisation dans Azure Search).

## <a name="backup-and-restore"></a>Sauvegarde et restauration

La Recherche Azure n’est pas une solution de stockage de données principal, c’est pourquoi nous ne fournissons pas de mécanisme formel de sauvegarde et de restauration en libre-service. Le code de votre application utilisé pour créer et remplir un index est l’option de restauration de facto si vous supprimez un index par erreur. 

Pour reconstruire un index, vous devez le supprimer (s’il existe), recréer l’index dans le service et le recharger en récupérant les données à partir de votre banque de données principale.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Augmentation ou réduction d'échelle
Au départ, chaque service de recherche comporte, au minimum, un réplica et une partition. Si vous avez souscrit à un [niveau qui fournit des ressources dédiées](search-limits-quotas-capacity.md), cliquez sur la vignette **METTRE À L’ÉCHELLE** dans le tableau de bord du service pour ajuster l’utilisation des ressources.

Lorsque vous ajoutez des capacités à travers l’une des ressources, le service les utilise automatiquement. Aucune autre action n’est nécessaire de votre part. Cependant, un léger décalage est à prévoir avant que l’effet de la nouvelle ressource soit perceptible. L’approvisionnement des ressources supplémentaires demande au moins 15 minutes.

 ![][10]

### <a name="add-replicas"></a>Ajout de réplicas
Pour augmenter le nombre de requêtes par seconde (RPS) ou parvenir à une haute disponibilité, il convient d'ajouter des réplicas. Chaque réplica comporte une copie d’un index. L’ajout d’un réplica se traduit donc par un ou plusieurs index supplémentaires disponibles pour satisfaire les demandes de requête. Au moins 3 réplicas sont nécessaires pour la haute disponibilité (pour plus d’informations, consultez [Planification de la capacité](search-capacity-planning.md)).

Un service de recherche qui comporte davantage de réplicas peut équilibrer la charge des demandes de requête sur un plus grand nombre d'index. Pour un nombre de requêtes donné, le débit sera plus élevé si davantage de copies de l'index sont disponibles pour leur traitement. Si vous constatez une latence des requêtes, la mise en ligne des répliques supplémentaires aura, à n'en pas douter, un effet positif sur les performances.

Bien que l'ajout de réplicas à votre service entraîne une augmentation du débit des requêtes, celui-ci n'est pas exactement multiplié par deux ou par trois. Toutes les applications de recherche sont, en effet, soumises à des facteurs externes susceptibles d'affecter les performances des requêtes. La complexité des requêtes et la latence réseau, notamment, sont deux facteurs qui contribuent à la fluctuation des temps de réponse des requêtes.

### <a name="add-partitions"></a>Ajout de partitions
La plupart des applications de service intègrent le besoin de plusieurs réplicas plutôt que de plusieurs partitions. Vous pouvez ajouter des partitions lorsqu'un plus grand nombre de documents est nécessaire si vous êtes inscrit au service Standard. Le niveau De base ne fournit pas d’autres partitions.

Au niveau Standard, les partitions sont ajoutées par multiples de 12 (notamment 1, 2, 3, 4, 6 ou 12). Il s’agit d’un artefact de partitionnement. Un index est créé dans 12 fragments (ou shards) qui peuvent tous être stockés dans 1 partition ou répartis équitablement dans 2, 3, 4, 6 ou 12 partitions (un fragment par partition).

### <a name="remove-replicas"></a>Suppression de réplicas
Après une période de traitement de requêtes intensive, vous pouvez utiliser le curseur pour réduire le nombre de réplicas une fois la charge de requêtes de recherche revenue à la normale (à la fin d’une période de soldes, par exemple). Rien de plus ! La réduction du nombre de réplicas entraîne l'abandon des machines virtuelles dans le centre de données. Désormais, vos opérations de requête et d'ingestion de données s'exécuteront sur un nombre moins élevé de machines virtuelles. La configuration minimale nécessite un réplica.

### <a name="remove-partitions"></a>Suppression de partitions
Contrairement à la suppression de réplicas, qui n'exige aucune opération supplémentaire de votre part, utiliser plus de volume de stockage que la capacité disponible après réduction peut entraîner une surcharge de travail. Par exemple, si votre solution utilise trois partitions, le fait de passer à une ou deux partitions générera une erreur si le nouvel espace de stockage est inférieur à celui nécessaire pour héberger votre index. Comme vous pouvez vous y attendre, deux solutions s’offrent alors à vous : soit supprimer des index ou documents au sein d’un index associé afin de libérer de l’espace, soit conserver la configuration actuelle.

Aucune méthode de détection ne vous permet d'identifier les fragments d'index qui sont stockés sur des partitions spécifiques. Chaque partition fournit environ 25 Go de stockage. Vous devrez donc réduire l'espace de stockage à une taille pouvant être prise en charge par le nombre de partitions dont vous disposez. Si vous souhaitez revenir à une seule partition, celle-ci devra contenir les 12 fragments.

Pour faciliter la planification, vous pouvez vérifier le stockage (voir la page [Obtenir des statistiques d'index](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) afin de connaître l'espace réellement utilisé. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Meilleures pratiques de mise à l’échelle et de déploiement
Cette vidéo de 30 minutes passe en revue les meilleures pratiques pour les scénarios de déploiement avancés, y compris les charges de travail géolocalisées. Vous pouvez également voir [Performances et optimisation dans Azure Search](search-performance-optimization.md) pour les pages d’aide qui couvrent les mêmes points.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Étapes suivantes
Dès lors que vous avez assimilé les concepts à la base de l’administration des services, envisagez d’utiliser [PowerShell](search-manage-powershell.md) pour automatiser les tâches.

Nous vous recommandons aussi de consulter l’[article relatif aux performances et à l’optimisation](search-performance-optimization.md).

Par ailleurs, nous vous conseillons de regarder la vidéo indiquée dans la section précédente. Elle décrit plus en détail les techniques mentionnées dans cette section.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



