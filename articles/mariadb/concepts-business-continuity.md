---
title: Continuité de l’activité - Azure Database for MariaDB
description: Découvrez en quoi consiste la continuité d’activité (limite de restauration dans le temps, interruption de centre de données, géorestauration) quand vous utilisez le service Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c01e0df1f420c8489ca3445d9fa025b251a870f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532389"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Comprendre la continuité d’activité dans Azure Database for MariaDB

Cet article décrit les fonctionnalités d’Azure Database for MariaDB en matière de continuité d’activité et de reprise d’activité après sinistre. Découvrez les options de reprise à la suite d’événements d’interruption susceptibles d’entraîner une perte de données ou une indisponibilité de votre base de données et de votre application. Connaissez la procédure à suivre lorsqu’un utilisateur ou qu’une erreur d’application affecte l’intégrité des données, lorsqu’une région Azure subit une panne ou que votre application nécessite une maintenance.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Fonctionnalités que vous pouvez utiliser pour garantir la continuité d’activité

Azure Database for MariaDB propose des fonctionnalités de continuité d’activité, notamment des sauvegardes automatisées et la possibilité pour les utilisateurs de lancer une géorestauration. Chacune de ces fonctionnalités présente des caractéristiques spécifiques concernant le temps de récupération estimé (ERT) et le risque de perte de données. Une fois que vous avez compris ces options, vous pouvez choisir celles qui vous conviennent et les utiliser ensemble dans différents scénarios. Au moment d’élaborer votre plan de continuité d’activité, vous devez comprendre le délai maximal acceptable nécessaire à la récupération complète de l’application après l’événement d’interruption, c’est-à-dire votre objectif de délai de récupération (RTO). Vous devez aussi comprendre la quantité maximale des récentes mises à jour de données (intervalle) que l’application peut accepter de perdre lors de la reprise après l’événement d’interruption, c’est-à-dire votre objectif de point de récupération (RPO).

Le tableau suivant compare l’ERT et le RPO pour les fonctionnalités disponibles :

| **Fonctionnalité** | **De base** | **Usage général** | **Mémoire optimisée** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Limite de restauration dans le temps à partir de la sauvegarde | N’importe quel point de restauration dans la période de rétention | N’importe quel point de restauration dans la période de rétention | N’importe quel point de restauration dans la période de rétention |
| Géo-restauration à partir de sauvegardes répliquées géographiquement | Non pris en charge | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Si vous supprimez le serveur, toutes les bases de données contenues dans le serveur sont également supprimées, sans pouvoir être restaurées. Vous ne pouvez pas restaurer un serveur supprimé.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Récupérer un serveur après une erreur d’utilisateur ou d’application

Vous pouvez vous servir des sauvegardes du service pour récupérer un serveur à la suite de plusieurs événements d’interruption. Un utilisateur peut supprimer par inadvertance des données, une table importante, voire une base de données tout entière. Une application peut accidentellement remplacer des données correctes par des données incorrectes en raison d’une défaillance d’application, etc.

Vous pouvez procéder à une restauration jusqu’à une date et heure pour créer une copie de votre serveur à un moment donné adéquat et connu. Ce moment donné doit se situer dans la période de rétention de sauvegarde que vous avez configurée pour votre serveur. Une fois les données restaurées sur le nouveau serveur, vous pouvez remplacer le serveur d’origine par le serveur nouvellement restauré ou copier les données nécessaires du serveur restauré sur le serveur d’origine.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Récupérer à la suite d’une panne du centre de données régional Azure

Bien que le fait soit rare, un centre de données Azure peut subir une panne. En cas de panne, il s’ensuit une interruption d’activité dont la durée peut se limiter à quelques minutes, mais aussi se compter en heures.

Vous pouvez attendre que votre serveur redevienne disponible une fois la panne réparée au niveau du centre de données. Cette solution vaut pour les applications qui peuvent s’accommoder d’un certain temps d’indisponibilité du serveur, par exemple dans un environnement de développement. Quand un centre de données subit une panne, vous ne savez pas combien de temps cela peut durer. Cette solution n’est donc valable que si vous n’avez pas besoin du serveur pendant un certain temps.

L’autre solution consiste à utiliser la fonctionnalité de géorestauration d’Azure Database for MariaDB qui restaure le serveur à partir de sauvegardes géoredondantes. Ces sauvegardes sont accessibles même si la région dans laquelle votre serveur est hébergé n’est pas disponible. Vous pouvez effectuer une restauration à partir de ces sauvegardes dans n’importe quelle autre région et remettre votre serveur en ligne.

> [!IMPORTANT]
> La géorestauration n’est possible que si vous avez provisionné le serveur avec le stockage de sauvegardes géoredondantes.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir plus en détail les sauvegardes automatisées, consultez [Sauvegardes dans Azure Database for MariaDB](concepts-backup.md).
- Pour effectuer une restauration à un point dans le temps à l’aide du portail Azure, consultez  [Restauration d’une base de données à un point dans le temps à l’aide du portail Azure](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
