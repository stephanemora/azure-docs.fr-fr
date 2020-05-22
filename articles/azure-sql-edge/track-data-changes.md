---
title: Suivi des modifications de données dans Azure SQL Edge (préversion)
description: En savoir plus sur le suivi des modifications et la capture des changements de données dans Azure SQL Edge (préversion)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a91ed5d4763aa521c9f6ed913dc532b08e37039f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594448"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Suivi des modifications de données dans Azure SQL Edge (préversion)

Azure SQL Edge prend en charge les deux fonctionnalités SQL Server qui suivent les modifications apportées aux données d’une base de données : [Suivi des modifications](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) et [Capture des changements de données](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Ces fonctionnalités permettent aux applications de déterminer les modifications de DML (opérations d’insertion, de mise à jour et de suppression) apportées aux tables utilisateur dans une base de données. La capture de données modifiées et le suivi des modifications peuvent être activés sur la même base de données ; aucune attention particulière n'est requise.

Certaines applications doivent, pour être efficaces, être en mesure de rechercher les données qui ont été modifiées dans une base de données. En règle générale, les développeurs d'applications doivent, pour identifier les données modifiées, implémenter une méthode de suivi personnalisée dans leurs applications en utilisant une combinaison de déclencheurs, de colonnes d'horodateur et de tables supplémentaires. La création de ces applications demande généralement un effort considérable, aboutit à des mises à jour de schéma et se traduit souvent par une importante diminution des performances. Dans le cas d’une solution IoT, impliquant le déplacement régulier de données de la périphérie vers un cloud ou centre de données, le suivi des modifications peut s'avérer particulièrement utile. Cela permet d’interroger rapidement et efficacement les seules modifications delta à partir de la dernière synchronisation et de charger ces modifications dans le cloud ou centre de données cible. Pour plus d’informations sur les avantages liés à l’utilisation du suivi des modifications et de la capture des changements de données, consultez [Avantages liés à l'utilisation de la capture des changements de données ou du suivi des modifications](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Pour comprendre les différences de fonctionnalités entre le suivi des modifications et la capture des changements de données, consultez [Différences de fonctionnalités entre la capture de données modifiées et le suivi des modifications](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Capture de données modifiées

Pour comprendre le fonctionnement de la capture des changements de données, consultez [À propos de la capture des changements de données](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Pour savoir comment activer ou désactiver la capture des changements de données, consultez [Activer et désactiver la capture des changements de données](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server).

Pour administrer et surveiller la capture des changements de données, consultez [Administrer et surveiller la capture des changements de données](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)

Pour savoir comment interroger et utiliser les données modifiées, consultez [Utiliser des données modifiées](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server)

## <a name="change-tracking"></a>Suivi des modifications

Pour comprendre le fonctionnement du suivi des modifications, consultez [À propos du suivi des modifications](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Pour savoir comment activer ou désactiver le suivi des modifications, consultez [Activer et désactiver le suivi des modifications](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

Pour administrer, surveiller et gérer le suivi des modifications, consultez [Administrer et surveiller le suivi des modifications](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

Pour savoir comment interroger et utiliser les données modifiées, consultez [Utiliser des données modifiées](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)

## <a name="temporal-tables"></a>Tables temporelles

Outre la prise en charge des fonctionnalités de suivi de modification et de capture des changements de données de SQL Server, Azure SQL Edge prend également en charge la fonctionnalité Tables temporelles de SQL Server. Les tables temporelles (également appelées tables temporelles avec version gérée par le système) en tant que fonctionnalité de base de données fournissent une prise en charge intégrée pour l’apport d’informations sur les données stockées dans la table à tout moment dans le temps, et non uniquement les données correctes au moment actuel.

Une table temporelle avec version contrôlée par le système est un type de table utilisateur conçu pour conserver un historique complet des modifications apportées aux données et permettre l’analyse à un point dans le temps. Ce type de table temporelle est appelée table temporelle avec version gérée par le système, car la période de validité de chaque ligne est gérée par le système (à avoir, le moteur de base de données).

Chaque table temporelle contient deux colonnes définies explicitement, chacune d’elles contenant un type de données datetime2 . Ces colonnes sont appelées colonnes de période. Ces colonnes de période sont utilisées de manière exclusive par le système pour enregistrer la période de validité de chaque ligne lorsqu’une ligne est modifiée.

En plus de ces colonnes de période, une table temporelle contient également une référence à une autre table avec un schéma en miroir. Le système utilise cette table pour stocker automatiquement la version précédente de la ligne chaque fois qu’une ligne de la table temporelle est mise à jour ou supprimée. Cette table supplémentaire est appelée table d’historique. La table principale qui stocke les versions de ligne actuelles (réelles) est appelée table actuelle ou simplement table temporelle. Lors de la création d’une table temporelle, les utilisateurs peuvent spécifier une table d’historique existante (qui doit être compatible avec le schéma) ou laisser le système créer une table d’historique par défaut.

Pour plus d’informations sur les tables temporelles, consultez [Tables temporelles](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables).

## <a name="see-also"></a>Voir aussi

- [Streaming de données dans Azure SQL Edge (préversion)](stream-data.md)
- [Machine Learning et IA avec ONNX dans Azure SQL Edge (préversion)](onnx-overview.md)
- [Configurer la réplication sur Azure SQL Edge (préversion)](configure-replication.md)
- [Sauvegarder et restaurer des bases de données dans Azure SQL Edge (préversion)](backup-restore.md)



