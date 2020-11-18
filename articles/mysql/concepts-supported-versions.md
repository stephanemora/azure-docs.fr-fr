---
title: Versions prises en charge – Azure Database pour MySQL
description: Découvrez les versions du serveur MySQL qui sont prises en charge dans le service Azure Database pour MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 0c4836ad48426bc5f2c8c18b888f0c296a90f222
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93417688"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versions prises en charge du serveur de base de données Azure pour MySQL

Azure Database for MySQL a été développé à partir de [MySQL Community Edition](https://www.mysql.com/products/community/), avec le moteur InnoDB.

MySQL utilise le schéma de nommage X.Y.Z. X correspond à la version majeure, Y à la version mineure et Z à la version du correctif de bogue. Pour plus d’informations sur ce schéma, reportez-vous à la [documentation MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).


> [!NOTE]
> Dans le service, une passerelle est utilisée pour rediriger les connexions vers des instances de serveur. Une fois la connexion établie, le client de MySQL affiche la version de MySQL définie dans la passerelle, et non la version en cours d’exécution sur votre instance de serveur MySQL. Pour déterminer la version de votre instance de serveur MySQL, utilisez la commande `SELECT VERSION();` à l’invite de MySQL.

Azure Database pour MySQL prend actuellement en charge les versions suivantes :

## <a name="mysql-version-56"></a>MySQL version 5.6

Version du correctif de bogue : 5.6.47

Pour plus d’informations sur les améliorations et les correctifs de cette version, consultez les [notes de publication](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) MySQL.

## <a name="mysql-version-57"></a>MySQL version 5.7

Version du correctif de bogue : 5.7.29

Pour plus d’informations sur les améliorations et les correctifs de cette version, consultez les [notes de publication](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) MySQL.

## <a name="mysql-version-80"></a>MySQL Version 8.0

Version du correctif de bogue : 8.0.15

Pour plus d’informations sur les améliorations et les correctifs de cette version, consultez les [notes de publication](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) MySQL.

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau
Le service gère automatiquement les correctifs pour les mises à jour des versions des correctifs de bogues. Par exemple, 5.7.20 à 5.7.21.  

Les mises à niveau des versions majeures et mineures ne sont pas prises en charge. Par exemple, il n’est pas possible de mettre à niveau MySQL 5.6 vers MySQL 5.7. Si vous voulez effectuer une mise à niveau de 5.6 vers 5.7, prenez une [image mémoire et restaurez-la](./concepts-migrate-dump-restore.md) sur un serveur créé avec la nouvelle version du moteur.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la stratégie de version d’Azure Database pour MySQL, consultez [ce document](concepts-version-policy.md).
- Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](./concepts-pricing-tiers.md).
