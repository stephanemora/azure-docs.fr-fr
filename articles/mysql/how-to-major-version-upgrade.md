---
title: Mise à niveau de version majeure dans Azure Database pour MySQL – Serveur unique
description: Cet article explique comment mettre à niveau une version majeure pour Azure Database pour MySQL – Serveur unique.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: ea2dc877c7bc6db387985e7b5cd1153e195ab4f1
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509568"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Mise à niveau de version majeure dans Azure Database pour MySQL Serveur unique

> [!NOTE]
> Cet article contient des références au terme _esclave_, un terme que Microsoft n’utilise plus. Quand le terme sera supprimé du logiciel, nous le supprimerons de cet article.
>

> [!IMPORTANT]
> La mise à niveau de version majeure pour un serveur unique Azure Database pour MySQL est en préversion publique.

Cet article décrit comment vous pouvez mettre à niveau votre version majeure de MySQL sur place dans un serveur unique Azure Database pour MySQL.

Cette fonctionnalité permet aux clients d’effectuer des mises à niveau sur place de leurs serveurs MySQL 5.6 vers MySQL 5.7 en un clic de bouton sans devoir déplacer des données ou modifier une chaîne de connexion d’application.

> [!Note]
> * La mise à niveau de version majeure n’est disponible que pour une mise à niveau de version majeure de MySQL 5.6 vers MySQL 5.7.
> * Le serveur ne sera pas disponible pendant l’opération de mise à niveau. Par conséquent, il est recommandé d’effectuer les mises à niveau au cours de votre fenêtre de maintenance planifiée. Vous pouvez envisager d’[effectuer une mise à niveau de version majeure avec temps d’arrêt minimal de MySQL 5.6 vers MySQL 5.7 à l’aide d’un réplica en lecture.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Mise à niveau de version majeure de MySQL 5.6 à MySQL 5.7 avec le Portail Azure

Pour effectuer une mise à niveau de version majeure de votre serveur Azure Database pour MySQL 5.6 avec le Portail Azure, procédez comme suit.

> [!IMPORTANT]
> Nous vous recommandons de d’abord effectuer la mise à niveau sur la copie restaurée du serveur au lieu de procéder directement à la mise à niveau de la production. Consultez [Comment effectuer une restauration à un point dans le temps](howto-restore-server-portal.md#point-in-time-restore).

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur Azure Database pour MySQL 5.6 existant.

2. Dans la page **Vue d’ensemble**, cliquez sur le bouton **Mise à niveau** dans la barre d’outils.

3. Dans la section **Mise à niveau**, sélectionnez **OK** pour mettre à niveau le serveur Azure Database pour MySQL de la version 5.6 vers la version 5.7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database pour MySQL – Vue d’ensemble – Mise à niveau":::

4. Une notification confirme la réussite de la mise à niveau.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Mise à niveau de version majeure de MySQL 5.6 à MySQL 5.7 avec Azure CLI

Pour effectuer une mise à niveau de version majeure de votre serveur Azure Database pour MySQL 5.6 avec Azure CLI, procédez comme suit.

> [!IMPORTANT]
> Nous vous recommandons de d’abord effectuer la mise à niveau sur la copie restaurée du serveur au lieu de procéder directement à la mise à niveau de la production. Consultez [Comment effectuer une restauration à un point dans le temps](howto-restore-server-cli.md#server-point-in-time-restore).

1. Installez [Azure CLI pour Windows](/cli/azure/install-azure-cli) ou utilisez Azure CLI dans [Azure Cloud Shell](../cloud-shell/overview.md) pour exécuter les commandes de mise à niveau. 
 
   La version 2.16.0 ou une version ultérieure d’Azure CLI est nécessaire pour cette mise à niveau. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. Exécutez az version pour rechercher la version et les bibliothèques dépendantes installées. Pour effectuer une mise à niveau vers la dernière version, exécutez az upgrade.

2. Une fois la connexion établie, exécutez la commande [az mysql server upgrade](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) :

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   L’invite de commandes affiche le message « -Running ». Il disparaît une fois la mise à niveau de la version effectuée.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Effectuer une mise à niveau de version majeure de MySQL 5.6 vers MySQL 5.7 sur un réplica en lecture avec le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur de réplica en lecture Azure Database pour MySQL 5.6 existant.

2. Dans la page **Vue d’ensemble**, cliquez sur le bouton **Mise à niveau** dans la barre d’outils.

3. Dans la section **Mise à niveau**, sélectionnez **OK** pour mettre à niveau le serveur de réplica en lecture Azure Database pour MySQL de la version 5.6 vers la version 5.7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database pour MySQL – Vue d’ensemble – Mise à niveau":::

4. Une notification confirme la réussite de la mise à niveau.

5. Dans la page **Vue d’ensemble**, vérifiez que la version du serveur de réplica en lecture Azure Database pour MySQL est 5.7.

6. Maintenant, accédez à votre serveur principal et [effectuez-y une mise à niveau de version majeure](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal).

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>Effectuer une mise à niveau de version majeure avec temps d’arrêt minimal de MySQL 5.6 vers MySQL 5.7 à l’aide de réplicas en lecture

Vous pouvez effectuer une mise à niveau de version majeure avec temps d’arrêt minimal de MySQL 5.6 vers MySQL 5.7 en utilisant des réplicas en lecture. L’idée consiste à mettre à niveau le réplica en lecture de votre serveur vers la version 5.7, puis à basculer votre application pour qu’elle pointe vers le réplica en lecture et à faire de ce dernier un nouveau serveur principal.

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre instance Azure Database pour MySQL 5.6 existante.

2. Créez un [réplica en lecture](https://docs.microsoft.com/azure/mysql/concepts-read-replicas#create-a-replica) à partir de votre serveur principal.

3. [Mettez à niveau votre réplica en lecture](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) vers la version 5.7.

4. Une fois que vous avez vérifié que le serveur réplica est en cours d’exécution sur la version 5.7, déconnectez votre application de votre serveur principal.
 
5. Contrôlez l’état de la réplication et assurez-vous que le réplica est parfaitement en phase avec le serveur principal afin que toutes les données soient synchronisées, puis vérifiez qu’aucune nouvelle opération n’est effectuée sur le serveur principal.

   Appelez la commande [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) sur le serveur réplica pour afficher l’état de réplication.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Si l’état de `Slave_IO_Running` et `Slave_SQL_Running` est « yes » et que la valeur de `Seconds_Behind_Master` est « 0 », cela signifie que la réplication fonctionne correctement. `Seconds_Behind_Master` indique que le retard du réplica. Si la valeur est différente de « 0 », cela signifie que le réplica est en train de traiter les mises à jour. Une fois que vous avez vérifié que `Seconds_Behind_Master` est « 0 », vous pouvez arrêter la réplication.

6. Promouvez votre réplica en lecture en serveur principal en [arrêtant la réplication](https://docs.microsoft.com/azure/mysql/howto-read-replicas-portal#stop-replication-to-a-replica-server).

7. Pointez votre application vers le nouveau serveur principal (ancien réplica) qui exécute Server 5.7. Chaque serveur est associé à une chaîne de connexion unique. Mettez à jour votre application pour qu’elle pointe vers l’ancien réplica plutôt que le serveur source.

> [!Note]
> Ce scénario entraîne un temps d’arrêt au cours des étapes 4, 5 et 6 uniquement.


## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Quand cette fonctionnalité de mise à niveau sera-t-elle en disponibilité générale ? Nous avons dans notre environnement de production MySQL v5.6, que nous devons mettre à niveau.

La disponibilité générale de cette fonctionnalité est prévue avant l’abandon de MySQL v5.6. Toutefois, la fonctionnalité est prête pour la production et entièrement prise en charge par Azure. Vous pouvez donc l’exécuter en toute confiance dans votre environnement. Dans le cadre des meilleures pratiques recommandées, nous vous suggérons vivement de l’exécuter et de la tester d’abord sur une copie restaurée du serveur afin de pouvoir estimer le temps d’arrêt pendant la mise à niveau et d’effectuer un test de compatibilité des applications avant de l’exécuter en production. Pour plus d’informations, consultez [Procédure de restauration à un point dans le temps](howto-restore-server-portal.md#point-in-time-restore) afin de créer une copie de votre serveur à un point dans le temps. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Cela entraînera-t-il un temps d’arrêt du serveur et, le cas échéant, de combien de temps ?

Oui, le serveur n’est pas disponible pendant le processus de mise à niveau. Nous vous recommandons donc d’effectuer cette opération au cours de la fenêtre de maintenance planifiée. Le temps d’arrêt estimé dépend de la taille de la base de données, de la taille de stockage approvisionnée (IOPs approvisionnée) et du nombre de tables sur la base de données. La durée de la mise à niveau est directement proportionnelle au nombre de tables sur le serveur. Les mises à niveau des serveurs de référence SKU de base sont censées prendre plus de temps que sur une plateforme de stockage standard. Pour estimer le temps d’arrêt de votre environnement de serveur, nous vous recommandons d’effectuer la mise à niveau sur la copie restaurée du serveur. Envisagez d’[effectuer une mise à niveau de version majeure avec un temps d’arrêt minimal de MySQL 5.6 vers MySQL 5.7 à l’aide d’un réplica en lecture](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas).

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Que se passe-t-il si nous ne choisissons pas de mettre à niveau notre serveur MySQL v5.6 avant le 5 février 2021 ?

Vous pouvez continuer à exécuter votre serveur MySQL v5.6 comme auparavant. Azure **n’effectuera jamais** de mise à niveau forcée sur votre serveur. Toutefois, les restrictions documentées dans la [stratégie de version d’Azure Database pour MySQL](concepts-version-policy.md) s’appliquent.

## <a name="next-steps"></a>Étapes suivantes

Découvrez la [Stratégie de contrôle de version Azure Database pour MySQL](concepts-version-policy.md).