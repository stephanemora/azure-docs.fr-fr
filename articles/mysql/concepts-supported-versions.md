---
title: Versions prises en charge – Azure Database pour MySQL
description: Découvrez les versions du serveur MySQL qui sont prises en charge dans le service Azure Database pour MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8b85307f01a11366a2147c947f26658f548932e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467712"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versions prises en charge du serveur de base de données Azure pour MySQL

Azure Database pour MySQL a été développé à partir de [MySQL Community Edition](https://www.mysql.com/products/community/), avec le moteur de stockage InnoDB. Le service prend en charge toutes les versions majeures actuelles prises en charge par la communauté, à savoir MySQL 5.6, 5.7 et 8.0. MySQL utilise le schéma de nommage X.Y.Z, où X est la version majeure, Y la version mineure et Z la version du correctif de bogue. Pour plus d’informations sur ce schéma, reportez-vous à la [documentation MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Dans l’option de déploiement Serveur unique, une passerelle est utilisée pour rediriger les connexions vers des instances de serveur. Une fois la connexion établie, le client de MySQL affiche la version de MySQL définie dans la passerelle, et non la version en cours d’exécution sur votre instance de serveur MySQL. Pour déterminer la version de votre instance de serveur MySQL, utilisez la commande `SELECT VERSION();` à l’invite de MySQL.

Azure Database pour MySQL prend actuellement en charge les versions majeures et mineures suivantes de MySQL :


| Version | Serveur unique <br/> Version mineure actuelle |Serveur flexible (préversion) <br/> Version mineure actuelle  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL version 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (Mise hors service) | Non pris en charge|
|MySQL version 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL Version 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Découvrez la stratégie de prise en charge des versions pour les versions mises hors service dans la [documentation relative à la stratégie de prise en charge des versions](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql).

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau
Le service gère automatiquement les correctifs pour les mises à jour des versions des correctifs de bogues. Par exemple, 5.7.20 à 5.7.21.  

La mise à niveau des versions principales est actuellement prise en charge par le service pour les mises à niveau de MySQL v5.6 à v5.7. Pour plus d’informations, consultez [comment effectuer des mises à niveau des versions principales](how-to-major-version-upgrade.md). Si vous voulez effectuer une mise à niveau de la version 5.7 vers la version 8.0, nous vous recommandons d’effectuer [le vidage et la restauration](./concepts-migrate-dump-restore.md) sur un serveur créé avec la nouvelle version du moteur.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la stratégie de version d’Azure Database pour MySQL, consultez [ce document](concepts-version-policy.md).
- Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](./concepts-pricing-tiers.md).
