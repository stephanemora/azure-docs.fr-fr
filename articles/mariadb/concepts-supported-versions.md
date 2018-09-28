---
title: Versions prises en charge dans Azure Database for MariaDB
description: Décrit les versions prises en charge dans Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: aa83b9955839aaa03aa2ebf46c9e464dc75c8d3a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977500"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Versions du serveur Azure Database for MariaDB prises en charge
Azure Database for MariaDB a été développé à partir du [serveur MariaDB](https://downloads.mariadb.org/) open source, en utilisant le moteur InnoDB. Azure Database for MariaDB prend actuellement en charge la version suivante :

## <a name="mariadb-version-10217"></a>MariaDB version 10.2.17
Pour découvrir plus en détail les améliorations et les correctifs introduits dans MariaDB 10.2.17, reportez-vous à la [documentation MariaDB](https://downloads.mariadb.org/mariadb/10.2.17/).

> [!NOTE]
> Dans le service, une passerelle est utilisée pour rediriger les connexions vers des instances de serveur. Une fois la connexion établie, le client MySQL affiche la version de MariaDB définie dans la passerelle, et non la version en cours d’exécution sur votre instance de serveur MariaDB. Pour déterminer la version de votre instance de serveur MariaDB, utilisez la commande `SELECT VERSION();` à l’invite MySQL.

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau
Le service gère automatiquement les correctifs pour les mises à jour de version mineures.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](./concepts-pricing-tiers.md).