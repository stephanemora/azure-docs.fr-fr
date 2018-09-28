---
title: Vue d’ensemble du niveau Hyperscale dans Azure SQL Database | Microsoft Docs
description: Cette rubrique décrit le niveau de service Hyperscale du modèle d’achat basé sur le nombre de vCores dans Azure SQL Database, et explique en quoi il diffère des niveaux de service Usage général et Critique pour l’entreprise.
services: sql-database
author: CarlRabeler
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 09/23/2018
ms.author: carlrab
ms.openlocfilehash: b50d049d051a57ba12239d4619572dc0dbd7e32f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962962"
---
# <a name="what-is-the-hyperscale-service-tier-preview-in-azure-sql-database"></a>Qu’est-ce que le niveau de service Hyperscale (préversion) dans Azure SQL Database ?

Le niveau de service Hyperscale dans Azure SQL Database est le tout nouveau niveau de service du modèle d’achat basé sur le nombre de vCores. Ce niveau de service est un stockage hautement scalable et un niveau de performances de calcul qui tire partie de l’architecture Azure pour augmenter le stockage et les ressources de calcul d’une base de données SQL Azure bien au-delà des limites disponibles des niveaux Usage général et Critique pour l’entreprise.

## <a name="what-are-the-capabilities-of-the-hyperscale-service-tier"></a>Quelles sont les fonctionnalités du niveau de service Hyperscale

Le niveau de service Hyperscale dans Azure SQL Database fournit les fonctionnalités supplémentaires suivantes :

- Prise en charge d’une taille de base de données pouvant atteindre 100 To 
- Accélération des grandes sauvegardes de base de données (basées sur des instantanés de fichier)
- Accélération des restaurations de base de données (basées sur des instantanés de fichier) 
- Meilleures performances générales en raison d’un débit de journal plus élevé et de temps de validation de transaction plus rapides, quels que soient les volumes de données 
- Augmentation rapide du nombre de nœuds (scale out) : vous pouvez provisionner un ou plusieurs nœuds en lecture seule pour décharger votre charge de travail de lecture et les utiliser comme serveurs de secours
- Augmentation rapide de la puissance des ressources (scale up) : vous pouvez, en temps constant, augmenter la puissance de vos ressources de calcul pour prendre en charge des charges de travail lourdes quand vous en avez besoin, puis diminuer la puissance des ressources de calcul quand elles ne sont plus nécessaires. 

Le niveau de service Hyperscale supprime de nombreuses limites pratiques traditionnellement rencontrées dans les bases de données cloud. Là où la plupart des autres bases de données sont limitées par les ressources disponibles dans un seul nœud, les bases de données du niveau de service Hyperscale n’ont pas de limite. Avec son architecture de stockage flexible, vous pouvez faire évoluer la capacité de stockage en fonction des besoins. En fait, les bases de données Hyperscale sont créées sans taille maximale définie. Une base de données Hyperscale augmente en fonction des besoins, et vous êtes facturé uniquement pour la capacité que vous utilisez. Pour les charges de travail de lecture intensives, le niveau de service Hyperscale permet une expansion rapide en provisionnant des réplicas de lecture supplémentaires en fonction des besoins pour décharger les charges de travail de lecture. 

Par ailleurs, le temps nécessaire pour créer des sauvegardes de base de données ou pour augmenter ou diminuer la puissance n’est plus lié au volume de données de la base de données. Les bases de données Hyperscale peuvent être sauvegardées presque instantanément. Vous pouvez aussi mettre à l’échelle une base de données de plusieurs dizaines de téraoctets en quelques minutes. Cette fonctionnalité vous évite d’être limité par votre choix de configuration initial. 

Pour plus d’informations sur les tailles de calcul du niveau de service Hyperscale, consultez [Choix du niveau de service et des ressources de calcul, de mémoire, de stockage et d’E/S en fonction du nombre de vCores](sql-database-service-tiers-vcore.md).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>À qui est destiné le niveau de service Hyperscale

Le niveau de service Hyperscale est principalement destiné aux clients qui ont de grandes bases de données en local et veulent moderniser leurs applications en les déplaçant dans le cloud, ou aux clients qui sont déjà dans le cloud et sont limités par les restrictions en matière de taille maximale de base de données (1-4 To). Il est également destiné aux clients qui recherchent des hautes performances et une haute scalabilité pour le stockage et le calcul.

Le niveau de service Hyperscale prend en charge toutes les charges de travail SQL Server, mais il est principalement optimisé pour OLTP. Le niveau de service Hyperscale prend aussi en charge les charges de travail hybrides et analytiques (mini-Data Warehouse). 

> [!IMPORTANT]
> Les pools élastiques ne prennent pas en charge le niveau de service Hyperscale.

## <a name="architecture-distributing-functions-to-isolate-capabilities"></a>Architecture : Répartition des fonctions pour isoler les fonctionnalités

Contrairement aux moteurs de base de données traditionnels qui centralisaient toutes les fonctions de gestion de données dans un même emplacement/processus (même les bases de données distribuées en production aujourd'hui ont plusieurs copies d’un moteur de données monolithique), une base de données Hyperscale sépare le moteur de traitement des requêtes, où la sémantique des différents moteurs de données diffère, des composants qui fournissent un stockage à long terme et une durabilité pour les données. De cette façon, la capacité de stockage peut être facilement mise à l’échelle pour répondre aux besoins (la cible initiale est de 100 To). Comme les réplicas en lecture seule partagent les mêmes composants de calcul, aucune copie de données n’est nécessaire pour mettre en place un nouveau réplica accessible en lecture.

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


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les niveaux de service, consultez [Niveaux de service](sql-database-service-tiers.md)
- Pour plus d’informations sur les limites de ressources, consultez [Limites de ressources vCore](sql-database-resource-limits.md)
- Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](sql-database-features.md).
