---
title: Déploiement des mises à niveau d’applications - Azure SQL Database | Microsoft Docs
description: Découvrez comment utiliser la géo-réplication de la base de données SQL pour prendre en charge les mises à niveau en ligne de votre application cloud.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/29/2019
ms.openlocfilehash: 50f6f114a4d90f48218f751e1649e8694e664491
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295744"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Gestion des mises à niveau propagées d’applications cloud à l’aide d’une géoréplication active de SQL Database

Découvrez comment utiliser la [géoréplication](sql-database-auto-failover-group.md) active dans SQL Database pour permettre des mises à niveau propagées de votre application cloud. Une mise à niveau est une opération qui entraîne une interruption de service ; il est donc recommandé de l’intégrer à votre conception et à votre planification de la continuité des activités. Dans cet article, nous allons examiner deux méthodes différentes permettant d’orchestrer le processus de mise à niveau propagée, avant de présenter les avantages et inconvénients de chaque option. Dans le cadre de cet article, nous allons utiliser une application sous la forme d’un site web utilisant une base de données unique comme couche de données. Notre objectif est de mettre à niveau la version 1 de l’application vers la version 2 sans que cela ait une incidence significative sur l’expérience des utilisateurs finaux.

Lorsque vous évaluez les options de mise à niveau, vous devez tenir compte des facteurs suivants :

* Impact sur la disponibilité de l’application au cours des mises à niveau. Durée pendant laquelle la fonction de l’application risque d’être limitée ou détériorée.
* Possibilité de restauration en cas d’échec de la mise à niveau.
* Vulnérabilité de l’application dans l’éventualité où un sinistre se produirait indépendamment pendant la mise à niveau.
* Coût total du processus,  notamment les coûts de redondance et les coûts incrémentiels des composants temporaires utilisés par le processus de mise à niveau.

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Mise à niveau d’applications dont la récupération d’urgence repose sur des sauvegardes de base de données

Si votre application s’appuie sur des sauvegardes automatiques de la base de données et utilise la géo-restauration pour la récupération d’urgence, elle est déployée dans une seule région Azure. Pour minimiser l’interruption pour l’utilisateur final, vous allez créer un environnement intermédiaire dans cette région avec tous les composants d’application impliqués dans la mise à niveau. Le schéma suivant illustre l’environnement d’exploitation avant la mise à niveau. Le point de terminaison `contoso.azurewebsites.net` représente un emplacement de production de l’application web. Pour permettre la restauration de la mise à niveau, vous devez créer un emplacement intermédiaire avec une copie entièrement synchronisée de la base de données. Les étapes suivantes permettent de créer un environnement intermédiaire pour la mise à niveau :

1. Créez une base de données secondaire dans la même région Azure. Surveillez la base de données secondaire afin de déterminer si le processus d’amorçage est terminé (1).
2. Créez un emplacement de déploiement pour votre application web appelé « Intermédiaire ». Il sera enregistré dans DNS avec l’URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Notez que les étapes de préparation n’auront aucune incidence sur l’emplacement de production et qu’il pourra fonctionner en mode d’accès complet.
>  

![Configuration de la géoréplication d’une base de données SQL. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Une fois les étapes de préparation terminées, l’application est prête pour la mise à niveau. Le schéma suivant illustre les étapes impliquées dans le processus de mise à niveau.

1. Définissez la base de données primaire sur le mode lecture seule (3). Ce mode garantit que l’emplacement de production de l’application web (V1) restera en lecture seule au cours de la mise à niveau, ceci afin d’éviter une divergence des données entre les instances de base de données V1 et V2.  
2. Déconnexion de la base de données secondaire à l’aide du mode d’arrêt planifié (4). Cette étape permet de créer une copie indépendante entièrement synchronisée de la base de données primaire. Cette base de données est alors mise à niveau.
3. Configurez la base de données primaire en mode lecture-écriture et exécutez le script de mise à niveau (5).

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Si la mise à niveau s’est correctement déroulée, vous êtes maintenant prêt à basculer les utilisateurs finaux sur la copie mise à niveau de l’application. Elle devient alors un emplacement de production.  Ce basculement implique quelques étapes supplémentaires, comme l’illustre le schéma suivant.

1. Effectuez une opération de permutation entre les emplacements de production et intermédiaire de l’application web (6). Ceci bascule les URL des deux emplacements. `contoso.azurewebsites.net` pointe maintenant vers la version V2 du site web et de la base de données (environnement de production).  
2. Si vous n’avez plus besoin de la version V1, qui est devenue une copie intermédiaire après la permutation, vous pouvez mettre l’environnement intermédiaire hors service (7).

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Si la mise à niveau échoue, par exemple en raison d’une erreur dans le script de mise à niveau, l’emplacement intermédiaire doit être considéré comme compromis. Pour restaurer l’application telle qu’elle se trouvait avant la mise à niveau, restaurez l’accès complet à l’application dans l’emplacement de production. Les étapes sont indiquées sur le schéma suivant.

1. Définition de la copie de base de données en mode lecture-écriture (8). Ceci restaure fonctionnellement la V1 complète de la copie de production.
2. Effectuez l’analyse de la cause racine et mettez l’environnement intermédiaire hors service (9).

À ce stade, l’application est entièrement fonctionnelle et les étapes de la mise à niveau peuvent être répétées.

> [!NOTE]
> La restauration ne nécessite pas de modifications DNS car que vous n’avez pas encore effectué d’opération de permutation.

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Le principal **avantage** de cette option est qu’elle vous permet de mettre à niveau une application dans une seule région grâce à une série d’étapes simples. Le coût de la mise à niveau est relativement faible. L’**inconvénient** est que, si une défaillance irrémédiable se produit pendant la mise à niveau, vous devrez redéployer l’application dans une autre région et restaurer la base de données à partir de la sauvegarde à l’aide de la géo-restauration pour pouvoir rétablir l’application telle qu’elle se trouvait avant la mise à niveau. Ce processus entraîne des interruptions de service importantes.

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Mise à niveau d’applications dont la récupération d’urgence repose sur la géoréplication de la base de données

Si votre application s’appuie sur la géoréplication active ou des groupes de basculement pour garantir la continuité d’activité, elle est déployée dans au moins deux régions différentes, avec une base de données primaire active dans la région primaire et une base de données secondaire en lecture seule dans la région de sauvegarde. Outre les facteurs mentionnés précédemment, le processus de mise à niveau doit garantir que :

* l’application demeure constamment à l’abri des sinistres pendant le processus de mise à niveau ;
* les composants géo-redondants de l’application sont mis à niveau parallèlement aux composants actifs.

Pour atteindre ces objectifs, en plus d’utiliser les emplacements de déploiement d’application web, vous allez utiliser Azure Traffic Manager (ATM) à l’aide d’un profil de basculement avec un point de terminaison actif et un point de terminaison de sauvegarde.  Le schéma suivant illustre l’environnement d’exploitation avant la mise à niveau. Les sites web `contoso-1.azurewebsites.net` et `contoso-dr.azurewebsites.net` représentent un environnement de production de l’application avec redondance géographique complète. L’environnement de production comprend les composants suivants :

1. Emplacement de production de l’application web `contoso-1.azurewebsites.net` dans la région primaire (1)
2. Base de données primaire dans la région primaire (2) 
3. Une instance de secours de l’application web dans la région de sauvegarde (3)
4. Base de données secondaire géo-répliquée dans la région de sauvegarde (4)
5. Profil de performance Azure Traffic Manager avec point de terminaison en ligne `contoso-1.azurewebsites.net` et point de terminaison hors connexion `contoso-dr.azurewebsites.net`

Pour permettre la restauration de la mise à niveau, vous devez créer un environnement intermédiaire avec une copie entièrement synchronisée de l’application. Pour avoir la garantie que l’application sera capable de récupérer rapidement en cas de défaillance irrémédiable pendant le processus de mise à niveau, l’environnement intermédiaire doit également être géoredondant. Les étapes suivantes sont nécessaires pour créer un environnement intermédiaire pour la mise à niveau :

1. Déployez un emplacement intermédiaire de l’application web dans la région primaire (6)
2. Créez une base de données secondaire dans la même Azure primaire (7). Configurez l’emplacement intermédiaire de l’application web auquel se connecter. 
3. Créez une autre base de données secondaire géoredondante dans la région de sauvegarde en répliquant la base de données secondaire dans la région primaire (on parle alors de « géo-réplication chaînée ») (8).
3. Déployez un emplacement intermédiaire de l’instance d’application web dans la région de sauvegarde (9) et configurez-le pour connecter la géo-secondaire créée à l’étape (9).


> [!NOTE]
> Notez que les étapes de préparation n’auront aucun impact sur l’application dans l’emplacement de production et que celle-ci resta entièrement fonctionnelle en mode lecture-écriture.

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Une fois les étapes de préparation terminées, l’environnement intermédiaire est prêt pour la mise à niveau. Le schéma suivant illustre les étapes de la mise à niveau.

1. Configurez la base de données primaire dans l’emplacement de production en mode lecture seule (10). Ce mode garantit que la base de données de production (V1) ne changera pas au cours de la mise à niveau, ceci afin d’éviter une divergence des données entre les instances de base de données V1 et V2.  
2. Déconnectez la base de données secondaire se trouvant dans la même région à l’aide du mode d’arrêt planifié (11). Cette étape permet de créer une copie indépendante mais entièrement synchronisée de la base de données de production. Cette base de données est alors mise à niveau.
3. Exécutez le script de mise à niveau sur `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net` et la base de données primaire intermédiaire (12). Les modifications apportées à la base de données sont automatiquement répliquées sur la secondaire intermédiaire 

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Si la mise à niveau s’est correctement déroulée, vous êtes maintenant prêt à basculer les utilisateurs finaux sur la version V2 de l’application. Le schéma suivant illustre les étapes impliquées dans ce processus.

1. Effectuez une opération de permutation entre les emplacements de production et intermédiaire de l’application web dans la région primaire (13) et dans la région de sauvegarde (14). La version V2 de l’application est maintenant un emplacement de production avec une copie redondante dans la région de sauvegarde.
2. Vous pouvez mettre l’environnement intermédiaire hors service si vous n’avez plus besoin de la version V1 de l’application (15 et 16).  

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Si la mise à niveau échoue, par exemple en raison d’une erreur dans le script de mise à niveau, l’environnement intermédiaire doit être considéré dans un état incohérent. Pour restaurer l’application telle qu’elle se trouvait avant la mise à niveau, rétablissez l’utilisation de la version V1 de l’application dans l’environnement. Les étapes nécessaires sont indiquées sur le schéma suivant.

1. Configurez la copie de la base de données primaire dans l’emplacement de production en mode lecture-écriture (17). Ceci restaure fonctionnellement la V1 complète dans l’emplacement de production.
2. Effectuez l’analyse de la cause racine et réparez ou retirez l’environnement intermédiaire (18 et 19).

À ce stade, l’application est entièrement fonctionnelle et les étapes de la mise à niveau peuvent être répétées.

> [!NOTE]
> La restauration ne nécessite pas de modifications DNS car que vous n’avez pas effectué d’opération de permutation.

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Le principal **avantage** de cette option est qu’elle vous permet de mettre à niveau l’application et sa copie géo-redondant en parallèle sans compromettre votre continuité d’activité lors de la mise à niveau. L’ **inconvénient** est qu’elle implique une double redondance de chaque composant de l’application, ce qui augmente le coût total de l’opération. Elle implique également un flux de travail plus complexe.

## <a name="summary"></a>Résumé

Les deux méthodes de mise à niveau décrites dans cet article présentent certaines différences en termes de complexité et de coût, mais les deux visent à réduire la durée pendant laquelle l’utilisateur final est limité aux opérations en lecture seule. Cette durée dépend directement de la durée du script de mise à niveau. Elle ne dépend pas la taille de la base de données, du niveau de service que vous avez choisi, de la configuration du site web ou d’autres facteurs que vous ne pouvez pas facilement contrôler. Toutes les étapes de préparation sont dissociées de la procédure de mise à niveau et n’ont aucun impact sur l’application de production. L’efficacité du script de mise à niveau est essentielle pour déterminer l’expérience utilisateur au cours des mises à niveau. La meilleure façon de l’améliorer consiste donc à concentrer vos efforts sur la création d’un script de mise à niveau aussi efficace que possible.  

## <a name="next-steps"></a>Étapes suivantes

* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
* Pour en savoir plus sur la géoréplication active Azure SQL Database, consultez [Créer des bases de données secondaires accessibles en lecture à l’aide de la géoréplication active](sql-database-active-geo-replication.md).
* Pour en savoir plus sur les groupes de basculement Azure SQL Database, consultez [Utiliser les groupes de basculement automatique pour permettre le basculement transparent et coordonné de plusieurs bases de données](sql-database-auto-failover-group.md).
* Pour en savoir plus sur les emplacements de déploiement et l’environnement intermédiaire dans Azure App Service, consultez [Configurer des environnements intermédiaires dans Azure App Service](../app-service/deploy-staging-slots.md).  
* Pour en savoir plus les profils Azure Traffic Manager, consultez [Gestion d’un profil Azure Traffic Manager](../traffic-manager/traffic-manager-manage-profiles.md).  


