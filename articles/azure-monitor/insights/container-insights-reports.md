---
title: Rapports dans Container Insights
description: Décrit les rapports qui permettent d’analyser les données collectées par Container Insights.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: ca74521a08d4edaa498e00e6452d8f69912e4bb9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032792"
---
# <a name="reports-in-container-insights"></a>Rapports dans Container Insights
Les rapports dans Container Insights sont des [classeurs Azure](../visualize/workbooks-overview.md) prêts à l’emploi recommandés. Cet article décrit les différents rapports disponibles et explique comment y accéder.

## <a name="viewing-reports"></a>Affichage de rapports
À partir du menu **Azure Monitor** du portail Azure, sélectionnez **Conteneurs**. Sélectionnez **Insights** dans la section **Supervision**, choisissez un cluster, puis sélectionnez la page **Rapports**. 

[![Page Rapports](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>Créer un classeur personnalisé
Pour créer un classeur personnalisé basé sur l’un de ces classeurs, sélectionnez la liste déroulante **Voir les classeurs**, puis sélectionnez **Accéder à la galerie AKS** en bas de la liste déroulante. Pour plus d’informations sur les classeurs et sur l’utilisation des modèles de classeur, consultez [Classeurs Azure Monitor](../visualize/workbooks-overview.md).

[![Galerie AKS](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>Classeurs de nœuds

- **Capacité du disque** : Graphiques interactifs d’utilisation du disque pour chaque disque présenté au nœud au sein d’un conteneur selon les perspectives suivantes :

    - Pourcentage d’utilisation de disque pour tous les disques.
    - Espace disque disponible pour tous les disques.
    - Grille qui indique pour chaque disque des nœuds, son pourcentage d’espace utilisé, sa tendance du pourcentage d’espace utilisé, l’espace disque disponible (Gio) et la tendance de l’espace disque disponible (Gio). Lorsqu’une ligne est sélectionnée dans la table, le pourcentage d’espace utilisé et l’espace disque disponible (Gio) sont indiqués sous la ligne.

- **E/S disque** : Graphiques interactifs d’utilisation du disque pour chaque disque présenté au nœud au sein d’un conteneur selon les perspectives suivantes :

    - E/S disque totalisées pour tous les disques par octets lus/s, octets écrits/s, et octets lus et écrits/s.
    - Les huit graphiques de performances montrent des indicateurs de performance clés pour aider à mesurer et à identifier les goulots d’étranglement des E/S disque.

- **GPU** : Graphiques interactifs d’utilisation du GPU pour chaque nœud de cluster Kubernetes prenant en charge les GPU.

## <a name="resource-monitoring-workbooks"></a>Classeurs de supervision des ressources

- **Déploiements** : État de vos déploiements et de l’autoscaler de pods horizontaux, y compris l’autoscaler personnalisé. 
  
- **Détails de la charge de travail** : Graphiques interactifs présentant les statistiques de performances des charges de travail pour un espace de noms. Comprend plusieurs onglets :

  - Vue d’ensemble de l’utilisation du processeur et de la mémoire par pod.
  - État des pods ou des conteneurs, avec les tendances de redémarrage des pods, les tendances de redémarrage des conteneurs et l’état des conteneurs pour les pods.
  - Événements Kubernetes, avec un récapitulatif des événements concernant le contrôleur.

- **Kubelet** : Comprend deux grilles qui montrent les statistiques clés de fonctionnement des nœuds :

    - Grille de vue d’ensemble par nœud indiquant le nombre total d’opérations, le nombre total d’erreurs et les opérations réussies en pourcentage et en tendance pour chaque nœud.
    - Vue d’ensemble par type d’opération montrant pour chaque opération le nombre total d’opérations, le nombre total d’erreurs et les opérations réussies en pourcentage et en tendance.
## <a name="billing-workbooks"></a>Classeurs de facturation

- **Utilisation des données** : Permet de visualiser la source de vos données sans avoir à créer votre propre bibliothèque de requêtes à partir des informations de notre documentation. Ce classeur inclut des graphiques avec lesquels vous pouvez afficher des données facturables depuis les perspectives suivantes :

  - Total des données facturables ingérées par chaque solution, en Go
  - Données facturables ingérées par chaque journal de conteneur (journal des applications)
  - Données des journaux de conteneurs facturables ingérées par chaque espace de noms Kubernetes
  - Données des journaux de conteneurs facturables ingérées, par nom de cluster
  - Données des journaux de conteneurs facturables ingérées, par entrée logsource
  - Données de diagnostic facturables ingérées, par journal de diagnostic de nœud maître

## <a name="networking-workbooks"></a>Classeurs de réseaux

- **Configuration de NPM** :  Supervision de vos configurations réseau configurées via le gestionnaire de stratégies réseau.

  - Informations récapitulatives sur la complexité de la configuration globale.
  - Nombre de stratégies, de règles et de définitions créées au fil du temps, permettant ainsi de comprendre la relation entre ces trois types d’éléments et d’ajouter une dimension temporelle au débogage d’une configuration.
  - Nombre d’entrées dans tous les IPSets et chaque IPSet.
  - Performances des pires cas et des cas moyens de chaque nœud, pour l’ajout de composants à votre configuration réseau.

- **Réseau** : Graphiques interactifs d’utilisation du réseau pour la carte réseau de chaque nœud, et une grille qui présente les indicateurs de performance clés pour vous aider à mesurer les performances de vos cartes réseau.



## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les classeurs dans Azure Monitor, consultez [Classeurs Azure Monitor](../visualize/workbooks-overview.md).
