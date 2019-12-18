---
title: Questions fréquentes sur Azure Monitor pour machines virtuelles (version en disponibilité générale) | Microsoft Docs
description: Azure Monitor pour machines virtuelles est une solution d’Azure qui supervise à la fois l’intégrité et les performances du système d’exploitation de la machine virtuelle Azure. En outre, elle identifie automatiquement les composants des applications et leurs dépendances avec les autres ressources, et effectue le mappage de leurs communications respectives. Cet article répond aux questions courantes au sujet de la version en disponibilité générale.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/05/2019
ms.openlocfilehash: ec0580d3360a56fc6b779b8c0a69eb2a09b007db
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928926"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Questions fréquentes sur Azure Monitor pour machines virtuelles (version en disponibilité générale)

Ce FAQ sur la disponibilité générale couvre les modifications qui se produisent dans Azure Monitor pour machines virtuelles que nous préparons actuellement pour la version Mise à la disposition générale. 

## <a name="updates-for-azure-monitor-for-vms"></a>Mises à jour d’Azure Monitor pour machines virtuelles

Nous prévoyons de publier une nouvelle version d’Azure Monitor pour machines virtuelles en janvier 2020. Les clients qui activeront Azure Monitor pour machines virtuelles après cette publication recevront automatiquement la nouvelle version, tandis que les clients existants qui utilisent déjà Azure Monitor pour machines virtuelles seront invités à effectuer une mise à niveau. Ce FAQ et notre documentation vous aideront à effectuer une mise à niveau à grande échelle si vos déploiements sont volumineux sur plusieurs espaces de travail.

Avec cette mise à niveau, les données de performances d’Azure Monitor pour machines virtuelles sont stockées dans la même table `InsightsMetrics` qu’[Azure Monitor pour conteneurs](container-insights-overview.md), ce qui vous permet d’interroger plus facilement les deux jeux de données. De plus, cette table vous permet de stocker des jeux de données plus diversifiés que la table utilisée auparavant. Nos affichages sur les performances vont également être mis à jour pour utiliser cette nouvelle table.

Nous allons utiliser de nouveaux types de données pour nos jeux de données de connexion. Cette modification se produira en décembre 2019 et sera annoncée dans un blog de mise à jour Azure. Les données actuellement stockées dans `ServiceMapComputer_CL` et `ServiceMapProcess_CL`, qui sont des tables de journaux personnalisées, vont être transférées vers les types de données dédiés `VMComputer` et `VMProcess`. Le transfert vers des types de données dédiés leur permet d’être prioritaires pour l’ingestion des données, mais permet aussi de standardiser le schéma de table pour l’ensemble des clients.

Conscients de la gêne occasionnée par la mise à niveau des workflows pour les clients existants, nous avons choisi d’effectuer ce changement maintenant dans le cadre de la Préversion publique plutôt que de le faire plus tard après la mise en disponibilité générale.

## <a name="what-is-changing"></a>Qu’est-ce qui change ?

Actuellement, quand vous effectuez le processus d’intégration d’Azure Monitor pour machines virtuelles, vous activez la solution Service Map dans l’espace de travail où vous avez choisi de stocker vos données de supervision, puis vous configurez les compteurs de performances pour les données que nous collectons à partir de vos machines virtuelles. Nous allons publier une nouvelle solution, appelée **VMInsights**, qui offre des fonctionnalités supplémentaires pour la collecte des données, ainsi qu’un nouvel emplacement de stockage de ces données dans votre espace de travail Log Analytics.

Dans notre processus actuel d’utilisation des compteurs de performances dans votre espace de travail Log Analytics, les données collectées sont envoyées dans la table `Perf`. La nouvelle solution envoie les données dans une table nommée `InsightsMetrics`, qui est déjà utilisée par Azure Monitor pour conteneurs. Le schéma de cette table nous permet de stocker des métriques et des jeux de données de service supplémentaires qui ne sont pas compatibles avec le format de la table Perf.

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Que faire avec les compteurs de performances de mon espace de travail si j’installe la solution VMInsights ?

La méthode actuelle d’activation d’Azure Monitor pour machines virtuelles utilise des compteurs de performances dans votre espace de travail. La nouvelle méthode stocke ces données dans une nouvelle table, `InsightsMetrics`.

Une fois que nous aurons modifié notre interface utilisateur pour utiliser les données dans la table `InsightsMetrics`, nous mettrons à jour notre documentation et nous communiquerons cette annonce via différents canaux, notamment par l’affichage d’une bannière dans le Portail Azure. Pour le moment, vous pouvez choisir de désactiver ces [compteurs de performances](vminsights-enable-overview.md#performance-counters-enabled) dans votre espace de travail si vous n’en avez plus besoin. 

>[!NOTE]
>Si vous utilisez des règles d’alerte qui font référence à ces compteurs dans la table `Perf`, vous devez les mettre à jour pour qu’elles référencent les nouvelles données stockées dans la table `InsightsMetrics`. Consultez notre documentation pour obtenir et réutiliser des exemples de requêtes sur des journaux qui font référence à cette table.
>

Si vous préférez garder les compteurs de performances activés, les données ingérées et stockées dans la table `Perf` vous seront facturées, sur la base de [la tarification Log Analytics[(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Quel impact ce changement aura-t-il sur mes règles d’alerte ?

Si vous avez créé des [alertes de journal](../platform/alerts-unified-log.md) qui interrogent la table `Perf` en ciblant les compteurs de performances activés dans l’espace de travail, vous devez mettre à jour ces règles pour qu’elles fassent référence à la table `InsightsMetrics` à la place. Ce conseil s’applique aussi à toutes les règles de recherche dans les journaux qui utilisent `ServiceMapComputer_CL` et `ServiceMapProcess_CL`, car ces jeux de données sont transférés vers les tables `VMComputer` et `VMProcess`.

Nous mettrons à jour ces questions fréquentes et notre documentation pour inclure des exemples de règles d’alerte de recherche dans les journaux pour les jeux de données que nous collectons.

## <a name="how-will-this-affect-my-bill"></a>Comment cela va-t-il affecter ma facture ?

La facturation reste basée sur la quantité de données ingérées et conservées dans votre espace de travail Log Analytics.

Les données de performances des machines que nous collectons sont les mêmes, ont une taille similaire aux données que nous stockions dans la table `Perf` et représentent à peu près le même coût.

## <a name="what-if-i-only-want-to-use-service-map"></a>Puis-je utiliser uniquement la solution Service Map ?

Bien sûr. Des invites sur la prochaine mise à jour s’afficheront dans le portail Azure quand vous accéderez à Azure Monitor pour machines virtuelles. Une fois la nouvelle solution publiée, vous recevrez une invite vous demandant de mettre à niveau votre version. Si vous préférez utiliser la fonctionnalité [Maps](vminsights-maps.md) uniquement, vous pouvez choisir de ne pas effectuer la mise à niveau et de continuer à utiliser la fonctionnalité Maps dans Azure Monitor pour machines virtuelles et la solution Service Map accessible à partir de votre espace de travail ou de la vignette du tableau de bord.

Si vous choisissez d’activer manuellement les compteurs de performances dans votre espace de travail, vous pourrez peut-être voir les données de certains graphiques de performances à partir d’Azure Monitor. Après avoir publié la nouvelle solution, nous mettrons à jour nos graphiques de performances afin qu’ils fassent les requêtes sur les données stockées dans la table `InsightsMetrics`. Pour voir les données de cette table représentées dans ces graphiques, vous devrez effectuer la mise à niveau vers la nouvelle version d’Azure Monitor pour machines virtuelles.

Le déplacement des données de `ServiceMapComputer_CL` et `ServiceMapProcess_CL` a des répercussions sur Service Map et sur Azure Monitor pour machines virtuelles. Vous devez donc toujours planifier cette mise à jour.

Si vous choisissez de ne pas effectuer la mise à niveau vers la solution **VMInsights**, vous pourrez continuer à utiliser les versions précédentes de nos classeurs de performances qui font référence aux données de la table `Perf`.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Les jeux de données de Service Map seront-ils également stockés dans InsightsMetrics ?

Les jeux de données ne seront pas dupliqués si vous utilisez les deux solutions. Ces solutions partagent les jeux de données stockés dans les tables `VMComputer` (anciennement ServiceMapComputer_CL), `VMProcess` (anciennement ServiceMapProcess_CL), `VMConnection` et `VMBoundPort`, où sont stockés les jeux de données cartographiques que nous collectons.  

La table `InsightsMetrics` stockera les jeux de données de machine virtuelle, de processus et de service que nous collectons et sera remplie uniquement si vous utilisez Azure Monitor pour machines virtuelles et la solution VMInsights. La solution Service Map ne collectera ni ne stockera de données dans la table `InsightsMetrics`.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Serai-je facturé à la fois pour la solution Service Map et la solution VMInsights si j’utilise les deux dans mon espace de travail ?

Non, les deux solutions partagent les jeux de données cartographiques que nous stockons dans les tables `VMComputer` (anciennement ServiceMapComputer_CL), `VMProcess` (anciennement ServiceMapProcess_CL), `VMConnection` et `VMBoundPort`. Ces données ne vous seront donc pas facturées deux fois si vous utilisez les deux solutions dans votre espace de travail.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Si je supprime la solution Service Map ou VMInsights, les données associées seront-elles aussi supprimées ?

Non, les deux solutions partagent les jeux de données cartographiques que nous stockons dans les tables `VMComputer` (anciennement ServiceMapComputer_CL), `VMProcess` (anciennement ServiceMapProcess_CL), `VMConnection` et `VMBoundPort`. Si vous supprimez l’une des solutions, ces jeux de données identifient qu’il reste une solution en place qui utilise les données et les conserve dans l’espace de travail Log Analytics. Les données sont supprimées de votre espace de travail seulement si vous supprimez les deux solutions.

## <a name="when-will-this-update-be-released"></a>Quand cette mise à jour sera-t-elle publiée ?

Nous prévoyons de publier la mise à jour d’Azure Monitor pour machines virtuelles au début de janvier 2020. À mesure que nous approcherons de la date de publication en janvier, nous actualiserons le contenu de ce forum aux questions et nous vous enverrons des notifications dans le Portail Azure quand vous ouvrirez Azure Monitor.

## <a name="health-feature-is-in-limited-public-preview"></a>La fonctionnalité Intégrité est disponible en préversion publique limitée

Nos clients ont été nombreux à nous adresser d’excellents commentaires au sujet de notre fonctionnalité Intégrité pour les machines virtuelles. Cette fonctionnalité suscite beaucoup d’intérêt et d’enthousiasme en raison de son potentiel de prise en charge des workflows de supervision. Nous prévoyons d’effectuer toute une série de changements afin d’améliorer la fonctionnalité et de répondre aux attentes de nos clients. 

Afin de minimiser l’impact de ces changements pour les nouveaux clients, nous avons rendu cette fonctionnalité disponible en **préversion publique limitée**. Cette mise à jour a eu lieu en octobre 2019.

Nous prévoyons de relancer cette fonctionnalité Intégrité en 2020, après la mise à disposition générale d’Azure Monitor pour machines virtuelles.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Comment les clients existants accéderont-ils à la fonctionnalité Intégrité ?

Les clients existants qui utilisent la fonctionnalité Intégrité continueront d’y accéder normalement. En revanche, les nouveaux clients n’y auront pas accès.  

Pour accéder à la fonctionnalité, vous pouvez ajouter l’indicateur de fonctionnalité `feature.vmhealth=true` à l’URL du Portail Azure [https://portal.azure.com](https://portal.azure.com). Par exemple : `https://portal.azure.com/?feature.vmhealth=true`.

Vous pouvez aussi utiliser cette courte URL, qui définit l’indicateur de fonctionnalité automatiquement : [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Si vous faites partie des clients existants, vous pouvez continuer à utiliser la fonctionnalité Intégrité sur les machines virtuelles qui sont connectées à une configuration d’espace de travail existante dotée de la fonctionnalité Intégrité.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>J’utilise actuellement la fonctionnalité Intégrité des machines virtuelles et je souhaite la déployer dans un nouvel environnement

Si vous êtes un client existant qui utilisez déjà la fonctionnalité Intégrité et que vous souhaitez l’utiliser dans le cadre d’un nouveau déploiement, envoyez-nous une demande par e-mail à vminsights@microsoft.com pour obtenir des instructions.

## <a name="next-steps"></a>Étapes suivantes

Pour comprendre les exigences et les méthodes de monitoring des machines virtuelles, voir [Déployer Azure Monitor pour machines virtuelles](vminsights-enable-overview.md).
