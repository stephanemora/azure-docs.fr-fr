---
title: Continuité de l’activité - Azure Database pour MySQL
description: Découvrez en quoi consiste la continuité d’activité (limite de restauration dans le temps, interruption de centre de données, géo-restauration) quand vous utilisez le service Azure Database pour MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: a02457c177e509280a93d7e6939143da3f7e9a84
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641191"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---single-server"></a>Vue d’ensemble de la continuité d’activité avec le serveur flexible Azure Database pour MySQL - Serveur unique

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Cet article décrit les fonctionnalités d’Azure Database pour MySQL en matière de continuité d’activité et de récupération d’urgence. Découvrez les options de reprise à la suite d’événements d’interruption susceptibles d’entraîner une perte de données ou une indisponibilité de votre base de données et de votre application. Connaissez la procédure à suivre lorsqu’un utilisateur ou qu’une erreur d’application affecte l’intégrité des données, lorsqu’une région Azure subit une panne ou que votre application nécessite une maintenance.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Fonctionnalités que vous pouvez utiliser pour garantir la continuité d’activité

Au moment d’élaborer votre plan de continuité d’activité, vous devez comprendre le délai maximal acceptable nécessaire à la récupération complète de l’application après l’événement d’interruption, c’est-à-dire votre objectif de délai de récupération (RTO). Vous devez aussi comprendre la quantité maximale des récentes mises à jour de données (intervalle) que l’application peut accepter de perdre lors de la reprise après l’événement d’interruption, c’est-à-dire votre objectif de point de récupération (RPO).

Azure Database pour MySQL Single Server propose des fonctionnalités de continuité d’activité et de reprise d’activité, y compris des sauvegardes géoredondantes offrant la possibilité de lancer une géorestauration et le déploiement de réplicas en lecture dans une autre région. Chacune de ces fonctionnalités présente des caractéristiques spécifiques concernant le temps de récupération et le risque de perte de données. Avec la fonctionnalité de [Géorestauration](concepts-backup.md), un nouveau serveur est créé à l’aide des données de sauvegarde répliquées à partir d’une autre région. Le temps total nécessaire à la restauration et à la récupération dépend de la taille de la base de données et de la quantité de journaux à récupérer. La durée totale d’établissement du serveur varie entre quelques minutes et quelques heures. Avec les [réplicas en lecture](concepts-read-replicas.md), les journaux des transactions du serveur principal sont diffusés de façon asynchrone vers le réplica. En cas de panne de la base de données primaire en raison d’une défaillance au niveau de la zone ou de la région, le basculement vers le réplica permet de raccourcir le RTO et de réduire les pertes de données.

> [!NOTE]
> Le décalage entre le serveur principal et le réplica dépend de la latence entre les sites, de la quantité de données à transmettre et, plus important, de la charge de travail d’écriture du serveur principal. Des charges de travail d’écriture intensives peuvent entraîner un décalage significatif. 
>
> En raison de la nature asynchrone de la réplication utilisée pour les réplicas en lecture, ils **ne doivent pas** être considérés comme une solution à haute disponibilité (HA), car des décalages plus importants peuvent signifier des RTO et RPO plus élevés. Les réplicas en lecture ne peuvent être considérés comme une solution de haute disponibilité que pour les charges de travail pour lesquelles le décalage reste plus faible pendant les périodes de pointe et les périodes creuses de la charge de travail. Dans le cas contraire, les réplicas en lecture sont destinés à une véritable échelle de lecture pour les charges de travail intensives prêtes et pour les scénarios de récupération d’urgence.

Le tableau suivant compare le RTO et le RPO dans un scénario de **charge de travail classique** :

| **Fonctionnalité** | **De base** | **Usage général** | **Mémoire optimisée** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Limite de restauration dans le temps à partir de la sauvegarde | N’importe quel point de restauration dans la période de rétention <br/> RTO – Variable <br/>RPO < 15 min| N’importe quel point de restauration dans la période de rétention <br/> RTO – Variable <br/>RPO < 15 min | N’importe quel point de restauration dans la période de rétention <br/> RTO – Variable <br/>RPO < 15 min |
| Géo-restauration à partir de sauvegardes répliquées géographiquement | Non pris en charge | RTO – Variable <br/>RPO < 1 h | RTO – Variable <br/>RPO < 1 h |
| Réplicas en lecture | RTO – Quelques minutes* <br/>RPO < 5 min* | RTO – Quelques minutes* <br/>RPO < 5 min*| RTO – Quelques minutes* <br/>RPO < 5 min*|

 \* Le RTO et le RPO **peuvent être beaucoup plus élevés** dans certains cas en fonction de différents facteurs, notamment la latence entre les sites, la quantité de données à transmettre et, surtout, la charge de travail d’écriture de la base de données primaire.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Récupérer un serveur après une erreur d’utilisateur ou d’application

Vous pouvez vous servir des sauvegardes du service pour récupérer un serveur à la suite de plusieurs événements d’interruption. Un utilisateur peut supprimer par inadvertance des données, une table importante, voire une base de données tout entière. Une application peut accidentellement remplacer des données correctes par des données incorrectes en raison d’une défaillance d’application, etc.

Vous pouvez procéder à une restauration jusqu’à une date et heure pour créer une copie de votre serveur à un moment donné adéquat et connu. Ce moment donné doit se situer dans la période de rétention de sauvegarde que vous avez configurée pour votre serveur. Une fois les données restaurées sur le nouveau serveur, vous pouvez remplacer le serveur d’origine par le serveur nouvellement restauré ou copier les données nécessaires du serveur restauré sur le serveur d’origine.

> [!IMPORTANT]
> Une fois supprimés, les serveurs ne peuvent être restaurés que dans un délai de **cinq jours**, au terme duquel les sauvegardes sont supprimées. La sauvegarde de base de données est accessible et peut être restaurée uniquement à partir de l’abonnement Azure qui héberge le serveur. Pour restaurer un serveur supprimé, reportez-vous aux [étapes documentées](howto-restore-dropped-server.md). À l'issue du déploiement, pour protéger les ressources du serveur d'une suppression accidentelle ou de changements inattendus, les administrateurs peuvent utiliser des [verrous de gestion](../azure-resource-manager/management/lock-resources.md).

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Récupérer à la suite d’une panne du centre de données régional Azure

Bien que le fait soit rare, un centre de données Azure peut subir une panne. En cas de panne, il s’ensuit une interruption d’activité dont la durée peut se limiter à quelques minutes, mais aussi se compter en heures.

Vous pouvez attendre que votre serveur redevienne disponible une fois la panne réparée au niveau du centre de données. Cette solution vaut pour les applications qui peuvent s’accommoder d’un certain temps d’indisponibilité du serveur, par exemple dans un environnement de développement. Quand un centre de données subit une panne, vous ne savez pas combien de temps cela peut durer. Cette solution n’est donc valable que si vous n’avez pas besoin du serveur pendant un certain temps.

## <a name="geo-restore"></a>La géorestauration

La fonctionnalité de géorestauration permet de restaurer le serveur à l’aide de sauvegardes géoredondantes. Les sauvegardes sont hébergées dans la [région appairée](../best-practices-availability-paired-regions.md) de votre serveur. Ces sauvegardes sont accessibles même si la région dans laquelle votre serveur est hébergé n’est pas disponible. Vous pouvez effectuer une restauration à partir de ces sauvegardes dans n’importe quelle autre région et remettre votre serveur en ligne. Pour plus d’informations sur la géorestauration, consultez cet [article sur les concepts de sauvegarde et de restauration](concepts-backup.md).

> [!IMPORTANT]
> La géorestauration n’est possible que si vous avez provisionné le serveur avec le stockage de sauvegardes géoredondantes. Si vous souhaitez basculer des sauvegardes redondantes localement aux sauvegardes géoredondantes pour un serveur existant, vous devez effectuer une image mémoire de votre serveur existant en utilisant mysqldump et la restaurer vers un serveur nouvellement créé configuré avec des sauvegardes géoredondantes.

## <a name="cross-region-read-replicas"></a>Réplicas en lecture inter-régions

Vous pouvez utiliser des réplicas en lecture inter-régions pour améliorer la planification de la continuité d’activité et de la reprise d’activité. Les réplicas en lecture sont mis à jour de manière asynchrone à l’aide de la technologie de réplication des journaux des transactions de MySQL. Pour plus d’informations sur les réplicas en lecture, les régions disponibles et le basculement, consultez cet [article sur les concepts relatifs aux réplicas en lecture](concepts-read-replicas.md). 

## <a name="faq"></a>Questions fréquentes (FAQ)

### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Où Azure Database pour MySQL stocke-t-il les données client ?
Par défaut, Azure Database pour MySQL ne déplace pas ni ne stocke les données client en dehors de la région dans laquelle il est déployé. Toutefois, les clients peuvent s’ils le souhaitent choisir d’activer les [sauvegardes géoredondantes](concepts-backup.md#backup-redundancy-options) ou de créer un [réplica en lecture sur plusieurs régions](concepts-read-replicas.md#cross-region-replication) pour stocker les données dans une autre région.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [sauvegardes automatisées dans Azure Database pour MySQL](concepts-backup.md).
- Découvrez comment effectuer une restauration à l’aide du [portail Azure](howto-restore-server-portal.md) ou de l’interface [Azure CLI](howto-restore-server-cli.md).
- Apprenez-en davantage sur les [réplicas en lecture dans Azure Database pour MySQL](concepts-read-replicas.md).
