---
title: Vue d’ensemble du niveau Hyperscale dans Azure SQL Database | Microsoft Docs
description: Cet article décrit le niveau de service Hyperscale dans le modèle d’achat basé sur le nombre de vCores dans Azure SQL Database, et explique en quoi il diffère des niveaux de service Usage général et Critique pour l’entreprise.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 4db013b51657bb327c36d616a3743c46731cd19a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903791"
---
# <a name="hyperscale-service-tier-preview-for-up-to-100-tb"></a>Niveau de service Hyperscale (préversion) jusqu’à 100 To

Azure SQL Database est basé sur une architecture de moteur de base de données SQL Server. Celle-ci est ajustée pour l’environnement cloud afin de garantir une disponibilité de 99,99 % même en cas de panne d’infrastructure. Trois modèles d’architecture sont utilisés dans Azure SQL Database :

- Usage général/Standard 
- Critique pour l’entreprise/Premium
- Hyperscale

Le niveau de service Hyperscale dans Azure SQL Database est le tout nouveau niveau de service du modèle d’achat basé sur le nombre de vCores. Ce niveau de service est un stockage hautement scalable et un niveau de performances de calcul qui tire partie de l’architecture Azure pour augmenter le stockage et les ressources de calcul d’une base de données SQL Azure bien au-delà des limites disponibles des niveaux Usage général et Critique pour l’entreprise.

> [!IMPORTANT]
> Le niveau de service Hyperscale est actuellement disponible en préversion publique dans un nombre limité de régions Azure. Pour obtenir la liste complète des régions, consultez [Régions disponibles pour le niveau de service Hyperscale](#available-regions). Nous vous recommandons de ne pas exécuter des charges de travail de production dans des bases de données Hyperscale pour le moment. Vous ne pouvez pas mettre à jour une base de données Hyperscale vers d’autres niveaux de service. À des fins de test, nous vous recommandons de faire une copie de votre base de données actuelle et de mettre à jour la copie vers le niveau de service Hyperscale.
> [!NOTE]
> Pour en savoir plus sur les niveaux de service Usage général et Critique pour l’entreprise du modèle d’achat vCore, consultez les niveaux de service [Usage général](sql-database-service-tier-general-purpose.md) et [Critique pour l’entreprise](sql-database-service-tier-business-critical.md). Pour obtenir une comparaison du modèle d’achat basé sur le nombre de cœurs virtuelss avec le modèle d’achat basé sur des unités DTU, consultez [Ressources et modèles d’achat Azure SQL Database](sql-database-purchase-models.md).
> [!IMPORTANT]
> Le niveau de service Hyperscale est actuellement en préversion publique. Nous vous recommandons de ne pas exécuter des charges de travail de production dans des bases de données Hyperscale pour le moment. Vous ne pouvez pas mettre à jour une base de données Hyperscale vers d’autres niveaux de service. À des fins de test, nous vous recommandons de faire une copie de votre base de données actuelle et de mettre à jour la copie vers le niveau de service Hyperscale.

## <a name="what-are-the-hyperscale-capabilities"></a>Présentation des fonctionnalités Hyperscale

Le niveau de service Hyperscale dans Azure SQL Database fournit les fonctionnalités supplémentaires suivantes :

- Prise en charge d’une taille de base de données pouvant atteindre 100 To
- Sauvegardes de base de données quasi instantanées (basées sur des instantanés de fichiers stockés dans le stockage Blob Azure), quel que soit leur taille, sans impact des E/S sur le calcul   
- Restaurations de base de données rapides (basées sur des instantanés de fichiers) en minutes plutôt qu’en heures ou en jours (opération qui ne dépend pas de la taille des données)
- Meilleures performances générales en raison d’un débit de journal plus élevé et de temps de validation de transaction plus rapides, quels que soient les volumes de données
- Augmentation rapide du nombre de nœuds (scale out) : vous pouvez provisionner un ou plusieurs nœuds en lecture seule pour décharger votre charge de travail de lecture et les utiliser comme serveurs de secours
- Augmentation rapide de la puissance des ressources (scale up) : vous pouvez, en temps constant, augmenter la puissance de vos ressources de calcul pour prendre en charge des charges de travail lourdes quand vous en avez besoin, puis diminuer la puissance des ressources de calcul quand elles ne sont plus nécessaires.

Le niveau de service Hyperscale supprime de nombreuses limites pratiques traditionnellement rencontrées dans les bases de données cloud. Là où la plupart des autres bases de données sont limitées par les ressources disponibles dans un seul nœud, les bases de données du niveau de service Hyperscale n’ont pas de limite. Avec son architecture de stockage flexible, le stockage augmente en fonction des besoins. En fait, les bases de données Hyperscale sont créées sans taille maximale définie. Une base de données Hyperscale augmente en fonction des besoins, et vous êtes facturé uniquement pour la capacité que vous utilisez. Pour les charges de travail de lecture intensives, le niveau de service Hyperscale permet une expansion rapide en provisionnant des réplicas de lecture supplémentaires en fonction des besoins pour décharger les charges de travail de lecture.

Par ailleurs, le temps nécessaire pour créer des sauvegardes de base de données ou pour augmenter ou diminuer la puissance n’est plus lié au volume de données de la base de données. Les bases de données Hyperscale peuvent être sauvegardées presque instantanément. Vous pouvez aussi mettre à l’échelle une base de données de plusieurs dizaines de téraoctets en quelques minutes. Cette fonctionnalité vous évite d’être limité par votre choix de configuration initial.

Pour plus d’informations sur les tailles de calcul pour le niveau de service Hyperscale, consultez [Caractéristiques du niveau de service](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>À qui est destiné le niveau de service Hyperscale

Le niveau de service Hyperscale est principalement destiné aux clients qui ont de grandes bases de données en local et veulent moderniser leurs applications en les déplaçant dans le cloud, ou aux clients qui sont déjà dans le cloud et sont limités par les restrictions en matière de taille maximale de base de données (1-4 To). Il est également destiné aux clients qui recherchent des hautes performances et une haute scalabilité pour le stockage et le calcul.

Le niveau de service Hyperscale prend en charge toutes les charges de travail SQL Server, mais il est principalement optimisé pour OLTP. Le niveau de service Hyperscale prend aussi en charge les charges de travail hybrides et analytiques (mini-Data Warehouse).

> [!IMPORTANT]
> Les pools élastiques ne prennent pas en charge le niveau de service Hyperscale.

## <a name="hyperscale-pricing-model"></a>Modèle tarifaire Hyperscale

Le niveau de service Hyperscale est disponible uniquement dans le [modèle vCore](sql-database-service-tiers-vcore.md). Pour s’aligner sur la nouvelle architecture, le modèle tarifaire est légèrement différent des niveaux de service Usage général ou Critique pour l’entreprise :

- **Calcul** :

  Le prix unitaire du calcul Hyperscale est par réplica. Le prix [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) est automatiquement appliqué aux réplicas avec échelle lecture. Dans la préversion publique, nous créons par défaut deux réplicas par base de données Hyperscale.

- **Stockage** :

  Vous n’avez pas besoin de spécifier la taille maximale des données lors de la configuration d’une base de données Hyperscale. Dans le niveau Hyperscale, le stockage de votre base de données vous est facturé selon votre utilisation réelle. Le stockage est alloué de manière dynamique entre 5 Go et 100 TB, par incréments de 1 Go.  

Pour plus d’informations sur les tarifs Hyperscale, consultez [Tarifs Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="distributed-functions-architecture"></a>Architecture des fonctions distribuées

Contrairement aux moteurs de base de données traditionnels qui centralisaient toutes les fonctions de gestion de données dans un même emplacement/processus (même les bases de données distribuées en production aujourd'hui ont plusieurs copies d’un moteur de données monolithique), une base de données Hyperscale sépare le moteur de traitement des requêtes, où la sémantique des différents moteurs de données diffère, des composants qui fournissent un stockage à long terme et une durabilité pour les données. De cette façon, la capacité de stockage peut être facilement mise à l’échelle pour répondre aux besoins (la cible initiale est de 100 To). Comme les réplicas en lecture seule partagent les mêmes composants de calcul, aucune copie de données n’est nécessaire pour mettre en place un nouveau réplica accessible en lecture. Pendant la préversion, un seul réplica en lecture seule est pris en charge.

Le diagramme suivant illustre les différents types de nœuds dans une base de données Hyperscale :

![architecture](./media/sql-database-hyperscale/hyperscale-architecture.png)

Une base de données Hyperscale contient les différents types de nœuds suivants :

### <a name="compute-node"></a>Nœud de calcul

Comme le nœud de calcul héberge le moteur relationnel, c’est là que tous les éléments de langage, le traitement des requêtes et ainsi de suite, se produisent. Toutes les interactions utilisateur avec une base de données Hyperscale se produisent dans ces nœuds de calcul. Les nœuds de calcul ont des caches SSD (étiquetés RBPEX - Extension du pool de mémoires tampons durable dans le diagramme précédent) afin de réduire le nombre d’allers-retours sur le réseau nécessaires pour récupérer une page de données. Il existe un nœud de calcul principal où sont traitées toutes les charges de travail de lecture-écriture et les transactions. Il existe un ou plusieurs nœuds de calcul secondaires qui agissent comme des serveurs de secours pour le basculement, et comme des nœuds de calcul en lecture seule pour décharger les charges de travail de lecture (si cette fonctionnalité est souhaitée).

### <a name="page-server-node"></a>Nœud de serveur de pages

Les serveurs de pages sont des systèmes qui représentent un moteur de stockage scale-out.  Chaque serveur de pages est responsable d’un sous-ensemble de pages dans la base de données.  Nominalement, chaque serveur de pages contrôle 1 téraoctet de données. Aucune donnée n’est partagée sur plusieurs serveurs de pages (en dehors des réplicas qui sont conservés pour la redondance et la disponibilité). Le travail d’un serveur de pages est de servir à la demande des pages de base de données aux nœuds de calcul et d’actualiser les pages à mesure que les transactions mettent à jour les données. Les serveurs de pages sont actualisés en lisant les enregistrements du journal à partir du service de journalisation. Les serveurs de pages entretiennent aussi les caches SSD pour améliorer les performances. Le stockage à long terme des pages de données s’effectue dans le Stockage Azure pour une meilleure fiabilité.

### <a name="log-service-node"></a>Nœud de service de journal

Le nœud de service de journal accepte les enregistrements de journal du nœud de calcul principal, les conserve dans un cache durable et les transfère au reste des nœuds de calcul (afin qu’ils mettent à jour leurs caches) ainsi qu’aux serveurs de pages appropriés afin que les données y soient mises à jour. De cette façon, tous les changements de données dans le nœud de calcul principal sont propagés par le service de journal à tous les nœuds de calcul secondaires et les serveurs de pages. Enfin, les enregistrements de journal sont poussés vers un stockage à long terme dans le Stockage Azure, qui est un dépôt de stockage infini. Avec ce mécanisme, vous n’avez plus besoin de tronquer le journal. Le service de journal a aussi un cache local pour accélérer l’accès.

### <a name="azure-storage-node"></a>Nœud de stockage Azure

Le nœud de stockage Azure est la destination finale des données provenant des serveurs de pages. Ce stockage est utilisé pour la sauvegarde ainsi que pour la réplication entre régions Azure. Les sauvegardes sont constituées d’instantanés de fichiers de données. L’opération de restauration est rapide à partir de ces instantanés, et les données peuvent être restaurées à n’importe quel point dans le temps.

## <a name="backup-and-restore"></a>Sauvegarde et restauration

Comme les sauvegardes sont basées sur des instantanés de fichier, elles sont quasi instantanées. La séparation du stockage et du calcul permet de pousser l’opération de sauvegarde/restauration vers la couche de stockage afin de réduire la charge de traitement sur le nœud de calcul principal. Par conséquent, la sauvegarde de grandes bases de données n’impacte pas les performances du nœud de calcul principal. De même, les restaurations sont effectuées en copiant l’instantané de fichier et n’ont donc pas la taille d’une opération de données. Pour les restaurations dans le même compte de stockage, l’opération de restauration est rapide.

## <a name="scale-and-performance-advantages"></a>Avantages de scalabilité et de performances

Avec la possibilité d’ajouter ou de supprimer rapidement des nœuds de calcul en lecture seule, l’architecture Hyperscale apporte des fonctionnalités d’échelle horizontale en lecture significatives et peut aussi libérer le nœud de calcul principal pour qu’il traite davantage de demandes d’écriture. Par ailleurs, les nœuds de calcul peuvent être mis à l’échelle (augmentation ou diminution) rapidement grâce à la fonctionnalité de stockage partagé propre à l’architecture Hyperscale.

## <a name="create-a-hyperscale-database"></a>Créer une base de données Hyperscale

Une base de données de très grande échelle peut être créé à l’aide de la [Azure portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Les bases de données Hyperscale sont disponibles uniquement avec le [modèle d’achat basé sur le nombre de vCores](sql-database-service-tiers-vcore.md).

La commande T-SQL suivante crée une base de données Hyperscale. Vous devez spécifier l’édition et l’objectif du service dans l’instruction `CREATE DATABASE`.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrer une base de données Azure SQL vers le niveau de service Hyperscale

Vous pouvez déplacer vos bases de données SQL Azure existantes à très grande échelle à l’aide du [Azure portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Dans la préversion publique, il s’agit d’une migration unidirectionnelle. Vous ne pouvez pas déplacer des bases de données depuis Hyperscale vers un autre niveau de service. Nous vous recommandons d’effectuer une copie de vos bases de données de production et de migrer vers Hyperscale pour la preuve de concepts.

La commande T-SQL suivante déplace une base de données vers le niveau de service Hyperscale. Vous devez spécifier l’édition et l’objectif du service dans l’instruction `ALTER DATABASE`.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Se connecter à un réplica avec échelle lecture d’une base de données Hyperscale

Dans les bases de données Hyperscale, l’argument `ApplicationIntent` de la chaîne de connexion fournie par le client détermine si la connexion est routée vers le réplica en écriture ou vers un réplica secondaire en lecture seule. Si l’option `ApplicationIntent` est définie sur `READONLY` et que la base de données ne dispose pas de réplica secondaire, la connexion est routée vers le réplica principal et la valeur par défaut est le comportement `ReadWrite`.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="available-regions"></a>Régions disponibles

Le niveau de service Hyperscale est actuellement disponible en préversion publique dans les régions Azure suivantes : Est des États-Unis 1, est des États-Unis 2, ouest des États-Unis 2, centre des États-Unis, S de CentralU du Nord, Europe de l’ouest, Europe du Nord, est de l’Australie, Sud-est de l’Australie, Asie du Sud-est, est du Japon et Corée

## <a name="known-limitations"></a>Limites connues

| Problème | Description |
| :---- | :--------- |
| Le volet Gérer les sauvegardes d’un serveur SQL Database ne s’affiche pas. Les bases de données Hyperscale sont filtrées à partir du serveur SQL->  | Hyperscale a une méthode distincte pour la gestion des sauvegardes. Par conséquent, les paramètres Conservation à long terme et Conservation des sauvegardes dans le temps ne s’appliquent pas/sont non valides. En conséquence, les bases de données Hyperscale n’apparaissent pas dans le volet Gérer les sauvegardes. |
| Limite de restauration dans le temps | Une fois qu’une base de données est migrée dans le niveau de service Hyperscale, la limite de restauration dans le temps avant la migration n’est pas prise en charge.|
| Si la taille d’un fichier de base de données augmente pendant la migration en raison d’une charge de travail active et qu’il dépasse la limite de 1 To par fichier, la migration échoue | Atténuations : <br> - Si possible, migrez la base de données quand aucune charge de travail de mise à jour n’est en cours d’exécution.<br> - Réessayez la migration. Elle réussira tant que la limite de 1 To n’est pas dépassée pendant la migration.|
| Managed Instance n’est actuellement pas pris en charge | Non prise en charge pour le moment |
| La migration vers Hyperscale est actuellement une opération unidirectionnelle | Une fois qu’une base de données est migrée vers Hyperscale, elle ne peut pas être migrée directement vers un niveau de service non Hyperscale. À l’heure actuelle, la seule façon de migrer une base de données du niveau Hyperscale vers un niveau non Hyperscale consiste à effectuer une exportation/importation à l’aide d’un fichier BACPAC.|
| La migration de bases de données avec des objets en mémoire n’est actuellement pas prise en charge | Les objets en mémoire doivent être supprimés et recréés en tant qu’objets qui ne sont pas en mémoire avant de migrer une base de données vers le niveau de service Hyperscale.|
| Le suivi des modifications de données n’est pas pris en charge pour le moment. | Vous ne pouvez pas utiliser le suivi des modifications de données avec des bases de données Hyperscale.

## <a name="next-steps"></a>Étapes suivantes

- Pour consultez un forum aux questions sur Hyperscale, consultez [Questions fréquentes (FAQ) sur Hyperscale](sql-database-service-tier-hyperscale-faq.md).
- Pour plus d’informations sur les niveaux de service, consultez [Niveaux de service](sql-database-purchase-models.md)
- Pour plus d’informations sur les limites au niveau du serveur et de l’abonnement, consultez l’article [Vue d’ensemble des limites de ressources sur un serveur SQL Database](sql-database-resource-limits-database-server.md).
- Pour connaître les limites du modèle d’achat pour une base de données unique, consultez [Limites du modèle d’achat basé sur le nombre de vCores d’Azure SQL Database pour une base de données unique](sql-database-vcore-resource-limits-single-databases.md).
- Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](sql-database-features.md).
