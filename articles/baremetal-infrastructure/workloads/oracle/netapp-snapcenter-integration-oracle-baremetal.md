---
title: Intégration de SnapCenter pour Oracle sur l’infrastructure BareMetal
description: Découvrez comment utiliser les technologies d’instantané Oracle et NetApp pour créer des sauvegardes de récupération opérationnelles pour les bases de données Oracle sur l’infrastructure BareMetal.
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: d9cf6933287167da82b1a5ad4ab8848d5c34d404
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579886"
---
# <a name="snapcenter-integration-for-oracle-on-baremetal-infrastructure"></a>Intégration de SnapCenter pour Oracle sur l’infrastructure BareMetal

Ce guide pratique vous aide à utiliser les technologies d’instantané Oracle et NetApp pour créer des sauvegardes de récupération opérationnelles pour les bases de données Oracle. L’utilisation de captures instantanées n’est qu’une des nombreuses approches de la protection des données pour Oracle. Les instantanés peuvent limiter les temps d’arrêt et la perte de données lors de l’exécution d’une base de données Oracle sur l’infrastructure BareMetal. 

>[!IMPORTANT]
>SnapCenter prend en charge Oracle pour les applications SAP, mais ne fournit pas l’intégration des outils SAP BR\*Tools.

Bien qu’Oracle offre deux méthodes de sauvegarde différentes pour les captures instantanées, SnapCenter de NetApp ne prend en charge qu’une seule méthode : le mode de sauvegarde à chaud. Le mode de sauvegarde à chaud est la version traditionnelle de la sauvegarde et de la création de captures instantanées Oracle. Elle nécessite une interaction avec l’hôte Oracle pour placer temporairement la base de données dans un mode de sauvegarde à chaud afin de cataloguer correctement les journaux d’archivage. Le mode de sauvegarde à chaud permet également une plus grande interaction avec la base de données RMAN pour assurer un suivi plus approfondi des instantanés disponibles pour la récupération. 

Les articles de ce guide vous accompagnent lors de la création d’une infrastructure de récupération d’urgence et de récupération opérationnelle pour Oracle en mode de sauvegarde à chaud. La récupération d’urgence est la récupération d’une base de données ou d’une partie d’une base de données suite à un incident. Par exemple, il peut s’agir d’un disque incorrect ou d’une base de données endommagée. La récupération opérationnelle est une récupération à partir d’une autre urgence. Un exemple peut être la perte de quelques lignes de données, ce qui n’entrave pas votre activité.

## <a name="snapcenter-database-organization"></a>Organisation de base de données SnapCenter
SnapCenter organise les bases de données dans des groupes de ressources. Un groupe de ressources peut être une ou plusieurs bases de données avec la même stratégie de protection. Vous n’avez donc pas besoin de sélectionner des volumes individuels qui font partie de la sauvegarde.

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-database-resource-group.png" alt-text="Diagramme montrant comment SnapCenter organise les bases de données dans des groupes de ressources" border="false":::

## <a name="oracle-disaster-recovery-architecture"></a>Architecture de récupération d’urgence Oracle

Les sauvegardes à chaud Oracle sont divisées en deux sauvegardes différentes pour une restauration par progression à l’aide des journaux d’archivage après la restauration des fichiers de données. La protection des fichiers de données et de contrôle est effectuée sur une base « horaire », mais une fréquence de sauvegarde plus longue est acceptable. Plus les intervalles entre les sauvegardes sont longs, plus le temps de récupération est long.  

SnapCenter crée des instantanés locaux de la base de données dans les emplacements de fichier de données par défaut (nfs01). Les instantanés sont créés sur chaque système de fichiers qui héberge des fichiers de données ou des fichiers de contrôle. Ces sauvegardes garantissent la récupération rapide de la base de données. Elles ne protègent pas les données en cas de défaillance d’un site ou d’une défaillance de plusieurs disques. 

Le nombre d’instantanés « horaires » dépend des stratégies de sauvegarde définies. Une base de données Oracle dispose de 2-5 jours de capacité de récupération opérationnelle à partir de captures instantanées.
 
Un nombre suffisant de journaux d’archivage doit exister et est requis pour restaurer par progression la base de données Oracle à partir du point de récupération des fichiers de données le plus récent et viable. Utilisez des captures instantanées des journaux d’archivage pour réduire l’objectif de point de récupération (RPO) d’une base de données Oracle. Plus la fréquence des instantanés dans les journaux d’archivage est faible, plus le RPO est faible. L’intervalle de capture instantanée recommandé pour les journaux d’archivage est de 5 ou 15 minutes. L’intervalle de 5 minutes, plus court, a le RPO le plus rapide.  Un intervalle plus bref augmente la complexité, car davantage de captures instantanées doivent être gérées dans le cadre du processus de récupération.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer NetApp SnapCenter pour acheminer le trafic d’Azure vers des serveurs d’infrastructure Oracle BareMetal.

> [!div class="nextstepaction"]
> [Configurer SnapCenter pour router le trafic](set-up-snapcenter-to-route-traffic.md)
