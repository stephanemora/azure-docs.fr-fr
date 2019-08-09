---
title: Versions prises en charge dans Azure Database for MariaDB
description: Décrit les versions prises en charge dans Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065724"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Versions du serveur Azure Database for MariaDB prises en charge

Azure Database for MariaDB a été développé à partir du [serveur MariaDB](https://downloads.mariadb.org/) open source, en utilisant le moteur InnoDB. Azure Database for MariaDB prend actuellement en charge la version suivante :

## <a name="mariadb-version-102"></a>MariaDB version 10.2

Pour découvrir plus en détail les améliorations et les correctifs introduits dans MariaDB 10.2.23, reportez-vous à la [documentation MariaDB](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/).

## <a name="mariadb-version-103"></a>MariaDB version 10.3

Pour découvrir plus en détail les améliorations et les correctifs introduits dans MariaDB 10.3.14, reportez-vous à la [documentation MariaDB](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/).

> [!NOTE]
> Dans le service, une passerelle est utilisée pour rediriger les connexions vers des instances de serveur. Une fois la connexion établie, le client MySQL affiche la version de MariaDB définie dans la passerelle, et non la version en cours d’exécution sur votre instance de serveur MariaDB. Pour déterminer la version de votre instance de serveur MariaDB, utilisez la commande `SELECT VERSION();` à l’invite MySQL.

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau

Le service gère automatiquement les correctifs pour les mises à jour de version mineures.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](./concepts-pricing-tiers.md).
