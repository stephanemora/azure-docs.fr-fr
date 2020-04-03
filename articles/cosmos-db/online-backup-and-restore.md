---
title: Sauvegarde en ligne et restauration de données à la demande dans Azure Cosmos DB
description: Cet article décrit le fonctionnement de la sauvegarde automatique en ligne et de la restauration de données à la demande dans Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: f5cc4339d7d6dce6d49c8d3eb744fca7fa5774d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240432"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Sauvegarde en ligne et restauration de données à la demande dans Azure Cosmos DB

Azure Cosmos DB sauvegarde automatiquement vos données à intervalles réguliers. Les sauvegardes automatiques sont effectuées sans affecter les performances ou la disponibilité des opérations de base de données. Toutes les sauvegardes sont stockées séparément dans un service de stockage, et ces sauvegardes sont répliquées globalement pour garantir la résilience contre les sinistres régionaux. Les sauvegardes automatiques sont utiles dans les scénarios où vous supprimez ou mettez à jour accidentellement votre compte, base de données ou conteneur Azure Cosmos et où vous avez besoin ultérieurement de récupérer les données.

## <a name="automatic-and-online-backups"></a>Sauvegardes automatiques et en ligne

Avec Azure Cosmos DB, vos données et leurs sauvegardes sont rendues hautement redondantes et résilientes aux sinistres régionaux. Les étapes suivantes montrent comment Azure Cosmos DB sauvegarde les données :

* Azure Cosmos DB sauvegarde automatiquement votre base de données toutes les 4 heures et à tout moment, seules les dernières 2 sauvegardes sont stockées. Toutefois, si le conteneur ou la base de données est supprimé, Azure Cosmos DB conserve les captures instantanées existantes du conteneur ou de la base de données pendant 30 jours.

* Azure Cosmos DB stocke ces sauvegardes dans Stockage Blob Azure, tandis que les données réelles résident localement dans Azure Cosmos DB.

*  Pour garantir une latence faible, la capture instantanée de votre sauvegarde est stockée dans le stockage Blob Azure dans la même région que la région d’écriture actuelle (ou l’une des régions d’écriture, si vous avez une configuration multimaître) de votre compte de base de données Azure Cosmos DB. Pour assurer la résilience contre les sinistres régionaux, chaque capture instantanée de vos données de sauvegarde dans le stockage blob Azure est à nouveau répliqué vers une autre région par le biais du stockage géoredondant (GRS). La région vers laquelle la sauvegarde est répliquée dépend de votre région source et de la paire régionale associée à la région source. Pour plus d’informations, consultez la [liste des paires de régions Azure géoredondantes](../best-practices-availability-paired-regions.md). Vous ne pouvez pas accéder directement à cette sauvegarde. Azure Cosmos DB utilise cette sauvegarde uniquement si une restauration de sauvegarde est lancée.

* Les sauvegardes sont effectuées sans affecter les performances ou la disponibilité de votre application. Azure Cosmos DB effectue la sauvegarde des données en arrière-plan sans consommer de débit (RU) provisionné supplémentaire et sans affecter les performances ou la disponibilité de votre base de données.

* Si vous avez accidentellement supprimé ou endommagé vos données, vous devez contacter le [support Azure](https://azure.microsoft.com/support/options/) dans les 8 heures afin que l’équipe Azure Cosmos DB puisse vous aider à les restaurer à partir des sauvegardes.

L’image suivante montre comment un conteneur Azure Cosmos avec les trois partitions physiques principales dans la région USA Ouest est sauvegardé dans un compte Stockage Blob Azure distant dans la région USA Ouest, puis répliqué dans la région USA Est :

![Sauvegardes complètes périodiques de toutes les entités Cosmos DB dans Stockage Azure GRS](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Options pour gérer vos propres sauvegardes

Avec les comptes d’API SQL Azure Cosmos DB, vous pouvez également tenir à jour vos propres sauvegardes en adoptant l’une des approches suivantes :

* Utiliser [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) pour déplacer régulièrement les données vers un stockage de votre choix

* Utiliser le [flux de modification](change-feed.md) Azure Cosmos DB afin de lire régulièrement les données pour les sauvegardes complètes (ainsi que pour les modifications incrémentielles) et les stocker dans votre propre stockage

## <a name="backup-retention-period"></a>Période de rétention des sauvegardes

Azure Cosmos DB prend des captures instantanées de vos données toutes les quatre heures. À chaque instant, seuls les deux dernières captures instantanées sont conservées. Toutefois, si le conteneur ou la base de données est supprimé, Azure Cosmos DB conserve les captures instantanées existantes du conteneur ou de la base de données pendant 30 jours.

## <a name="restoring-data-from-online-backups"></a>Restauration des données à partir de sauvegardes en ligne

Une suppression ou modification accidentelle des données peut se produire dans l’un des scénarios suivants :  

* Le compte Azure Cosmos entier est supprimé

* Une ou plusieurs bases de données Azure Cosmos sont supprimées

* Un ou plusieurs conteneurs Azure Cosmos sont supprimés

* Des éléments Azure Cosmos (par exemple des documents) dans un conteneur sont supprimés ou modifiés. Ce cas spécifique est généralement appelé « altération des données ».

* Une base de données d’offre partagée ou des conteneurs au sein d’une base de données d’offre partagée sont supprimés ou endommagés

Azure Cosmos DB peut restaurer les données dans tous les scénarios ci-dessus. Le processus de restauration crée toujours un compte Azure Cosmos pour stocker les données restaurées. Le nom du nouveau compte, s’il n’est pas spécifié, aura le format `<Azure_Cosmos_account_original_name>-restored1`. Le dernier chiffre est incrémenté si plusieurs tentatives de restauration sont effectuées. Vous ne pouvez pas restaurer les données sur un compte Azure Cosmos créé au préalable.

Quand un compte Azure Cosmos est supprimé, nous pouvons restaurer les données dans un compte portant le même nom, à condition que le nom du compte ne soit pas en cours d’utilisation. Dans ce cas, nous vous recommandons de ne pas recréer le compte après la suppression, car non seulement cela empêche les données restaurées d'utiliser le même nom, mais cela rend également plus difficile la découverte du compte à restaurer. 

Quand une base de données Azure Cosmos est supprimée, il est possible de restaurer la base de données entière ou un sous-ensemble des conteneurs au sein de cette base de données. Il est également possible de sélectionner des conteneurs parmi différentes bases de données et de les restaurer tous. Toutes les données restaurées sont alors placées dans un nouveau compte Azure Cosmos.

Quand un ou plusieurs éléments dans un conteneur sont supprimés ou modifiés accidentellement (altération des données), vous devez spécifier le point dans le temps auquel effectuer la restauration. Ce point dans le temps a une importance cruciale. Le conteneur étant en ligne, la sauvegarde est toujours en cours d’exécution. Par conséquent, si vous attendez au-delà de la période de rétention (la valeur par défaut est de huit heures) les sauvegardes seront remplacées. Dans le cas des suppressions, vos données ne sont plus stockées car elles ne seront pas remplacées par le cycle de sauvegarde. Les sauvegardes des bases de données ou conteneurs supprimés sont conservées pendant 30 jours.

Si vous provisionnez le débit au niveau de la base de données (autrement dit, quand un ensemble de conteneurs partagent le débit provisionné), le processus de sauvegarde et de restauration se produit au niveau de la base de données entière et non au niveau des conteneurs individuels. Dans ce cas, la sélection d’un sous-ensemble de conteneurs à restaurer n’est pas une option.

## <a name="migrating-data-to-the-original-account"></a>Migration des données vers le compte d’origine

L’objectif principal de la restauration des données est d’offrir un moyen de récupérer toutes les données que vous avez supprimées ou modifiées accidentellement. Nous recommandons donc d’inspecter d’abord le contenu des données restaurées, afin de vous assurer qu’il correspond à vos attentes. Ensuite, procédez à la migration des données vers le compte principal. Bien qu'il soit possible d'utiliser le compte restauré en tant que compte actif, cette option n'est pas recommandée si vous avez des charges de travail de production.  

Voici différentes méthodes pour migrer les données vers le compte Azure Cosmos d’origine :

* Utilisation de l’[Outil de migration de données Cosmos DB](import-data.md)
* Utilisation d’[Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Utilisation du [flux de modification](change-feed.md) dans Azure Cosmos DB 
* Écriture de code personnalisé

Supprimez les comptes restaurés dès que vous avez terminé la migration, car ils occasionnent des frais.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant découvrir comment restaurer des données à partir d’un compte Azure Cosmos, ou comment migrer des données vers un compte Azure Cosmos.

* Pour effectuer une demande de restauration, [émettez un ticket à partir du portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* [Guide pratique pour restaurer des données à partir d’un compte Azure Cosmos](how-to-backup-and-restore.md)
* [Utiliser le flux de modification Cosmos DB](change-feed.md) pour déplacer des données vers Azure Cosmos DB.
* [Utiliser Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) pour déplacer des données vers Azure Cosmos DB.

