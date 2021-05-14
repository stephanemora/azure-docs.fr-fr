---
title: Sauvegarde et restauration - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Protection des données contre une altération ou une suppression accidentelle
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520171"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Sauvegarde et restauration dans Azure Database pour PostgreSQL - Hyperscale (Citus)

Azure Database pour PostgreSQL – Hyperscale (Citus) crée automatiquement des sauvegardes de chaque nœud et les conserve dans un stockage localement redondant. Les sauvegardes peuvent être utilisées pour restaurer votre groupe de serveurs Hyperscale (Citus) à une heure spécifiée.
La sauvegarde et la restauration sont une partie essentielle de toute stratégie de continuité d’activité, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles.

## <a name="backups"></a>Sauvegardes

Au moins une fois par jour, Azure Database pour PostgreSQL effectue des sauvegardes instantanées des fichiers de données et du journal des transactions de base de données. Les sauvegardes vous permettent de restaurer un serveur à n’importe quel point dans le temps au sein de la période de conservation. (La période de conservation est de 35 jours pour tous les groupes de serveurs.) Toutes les sauvegardes sont chiffrées en utilisant le chiffrement AES 256 bits.

Dans les régions Azure qui prennent en charge les zones de disponibilité, les instantanés de sauvegarde sont stockés dans trois zones de disponibilité. Tant qu’au moins une zone de disponibilité est en ligne, le groupe de serveurs Hyperscale (Citus) peut être restauré.

Les fichiers de sauvegarde ne peuvent pas être exportés. Ils sont utilisables uniquement pour les opérations de restauration dans Azure Database pour PostgreSQL.

### <a name="backup-storage-cost"></a>Coût du stockage de sauvegarde

Pour connaître les prix du stockage de sauvegarde, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/) d’Azure Database pour PostgreSQL - Hyperscale (Citus).

## <a name="restore"></a>Restaurer

Vous pouvez restaurer un groupe de serveurs Hyperscale (Citus) à n’importe quel point dans le temps au cours des 35 derniers jours.  La restauration à un point dans le temps est utile dans plusieurs scénarios. Par exemple, quand un utilisateur supprime accidentellement des données, perd une base de données ou une table importante ou si une application remplace accidentellement des données correctes par des données erronées.

> [!IMPORTANT]
> Les groupes de serveurs Hyperscale (Citus) supprimés ne peuvent pas être restaurés. Si vous supprimez le groupe de serveurs, tous les nœuds qui appartiennent au groupe de serveurs sont supprimés et ne peuvent pas être récupérés. À l’issue du déploiement, pour protéger les ressources du groupe de serveurs d’une suppression accidentelle ou de changements inattendus, les administrateurs peuvent utiliser des [verrous de gestion](../azure-resource-manager/management/lock-resources.md).

Le processus de restauration crée un groupe de serveurs dans la même région, le même abonnement et le même groupe de ressources Azure que l’original. Le groupe de serveurs a la configuration d’origine : le nombre de nœuds, le nombre de vCores, la taille de stockage, les rôles utilisateur, la version de PostgreSQL et la version de l’extension Citus sont les mêmes.

Les paramètres de pare-feu et les paramètres de serveur PostgreSQL ne sont pas conservés à partir du groupe de serveurs d’origine ; ils sont réinitialisés aux valeurs par défaut. Le pare-feu empêche toute connexion. Vous devez ajuster manuellement ces paramètres après la restauration. D’une façon générale, consultez notre liste des [tâches suggérées postérieures à la restauration](howto-hyperscale-restore-portal.md#post-restore-tasks).

## <a name="next-steps"></a>Étapes suivantes

* Consultez les étapes de [restauration d’un groupe de serveurs](howto-hyperscale-restore-portal.md) dans le portail Azure.
* Découvrez-en plus sur les  [zones de disponibilité Azure](../availability-zones/az-overview.md).
