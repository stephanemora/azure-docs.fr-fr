---
title: Limites de ressources des serveurs Azure SQL Database | Microsoft Docs
description: Cet article contient une vue d’ensemble des limites de ressources des serveurs Azure SQL Database pour les bases de données uniques et les pools élastiques. Il fournit également des informations concernant ce qui se passe lorsque ces limites de ressources sont atteintes ou dépassées.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 06e61d30f0d4e598c48f190572b8b4343f351043
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762787"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Limites de ressources SQL Database des serveurs Azure SQL Database

Cet article contient une vue d’ensemble des limites de ressources SQL Database d’un serveur SQL Database qui gère des bases de données uniques et des pools élastiques. Il fournit également des informations concernant ce qui se passe lorsque ces limites de ressources sont atteintes ou dépassées.

> [!NOTE]
> Pour prendre connaissance des limites Managed Instance, consultez l’article [Vue d’ensemble des limites de ressources Azure SQL Database Managed Instance](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites maximales de ressources

| Ressource | Limite |
| :--- | :--- |
| Bases de données par serveur | 5 000 |
| Nombre par défaut de serveurs par abonnement dans n’importe quelle région | 20 |
| Nombre maximal de serveurs par abonnement dans n’importe quelle région | 200 |  
| Quota DTU/eDTU par serveur | 54 000 |  
| Quota vCore par serveur/instance | 540 |
| Nombre maximal de pools par serveur | Limité par le nombre de DTU ou de vCores. Par exemple, si chaque pool contient 1 000 unités DTU, alors un serveur peut prendre en charge 54 pools.|
|||

> [!NOTE]
> Pour augmenter le quota DTU/eDTU ou vCore ou pour obtenir une quantité de serveurs supérieure à la quantité par défaut, vous pouvez envoyer une nouvelle demande de support dans le Portail Azure pour l’abonnement en indiquant le type de problème « Quota ». Le quota DTU/eDTU et le nombre maximal de bases de données par serveur limitent le nombre de pools élastiques par serveur.
> [!IMPORTANT]
> Le nombre de bases de données approchant la limite par serveur SQL Database, les éléments suivants peuvent se produire :
> - Augmentation de latence dans l’exécution de requêtes sur la base de données master.  Cela inclut les vues de statistiques d’utilisation des ressources telles que sys.resource_stats.
> - Augmentation de latence des opérations de gestion et le rendu des points de vue de portails qui impliquent des bases de données sur le serveur.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Que se passe-t-il lorsque les limites de ressources d’une base de données sont atteintes ?

### <a name="compute-dtus-and-edtus--vcores"></a>Compute (DTU et eDTU / vCore)

Lorsque l’utilisation du calcul de la base de données (exprimée en DTU, eDTU ou vCore) est élevée, la latence de la requête augmente et peut même causer une expiration du délai d’attente. Dans ces conditions, les requêtes peuvent être mises en file d’attente par le service et reçoivent des ressources pour leur exécution lorsque des ressources se libèrent.
En cas d’utilisation élevée des calculs, voici certaines des options d’atténuation à votre disposition :

- Augmenter la taille de calcul de la base de données ou du pool élastique pour fournir plus de ressources de calcul à la base de données. Consultez [Mise à l’échelle des ressources d’une base de données unique](sql-database-single-database-scale.md) et [Mise à l'échelle des ressources d’un pool élastique](sql-database-elastic-pool-scale.md).
- Optimiser les requêtes pour réduire l’utilisation des ressources de chaque requête. Pour plus d’informations, consultez la page [Paramétrage/Compréhension de requêtes](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Stockage

Lorsque l’espace de base de données utilisé atteint la limite de taille maximale, les insertions et mises à jour de base de données qui augmentent la taille des données échouent et les clients reçoivent un [message d’erreur](sql-database-develop-error-messages.md). Les opérations de type SELECT et DELETE sur la base de données continuent à aboutir.

En cas d’utilisation élevée de l’espace, voici certaines des options d’atténuation à votre disposition :

- Augmenter la taille maximale de la base de données ou du pool élastique, ou ajouter plus de stockage. Consultez [Mise à l’échelle des ressources d’une base de données unique](sql-database-single-database-scale.md) et [Mise à l'échelle des ressources d’un pool élastique](sql-database-elastic-pool-scale.md).
- Si la base de données est dans un pool élastique, vous pouvez également déplacer la base de données en dehors du pool afin que son espace de stockage ne soit pas partagé avec d’autres bases de données.
- Réduire une base de données afin de récupérer l’espace inutilisé. Pour plus d’informations, consultez [Gérer l’espace des fichiers dans Azure SQL Database](sql-database-file-space-management.md).

### <a name="sessions-and-workers-requests"></a>Sessions et workers (requêtes)

Le nombre maximal de sessions et de workers est déterminé par le niveau de service et la taille de calcul (DTU et eDTU). Lorsque les limites de sessions ou de workers sont atteintes, les nouvelles requêtes sont refusées et les clients reçoivent un message d’erreur. Si le nombre de connexions disponibles peut être contrôlé par l’application, le nombre de workers simultanés est souvent plus difficile à estimer et à contrôler. Cela est particulièrement vrai pendant les pics de charge lorsque les limites de ressources de base de données sont atteintes et que les workers s’accumulent en raison de requêtes plus longues à exécuter.

En cas d’utilisation élevée de workers ou de sessions, voici certaines des options d’atténuation à votre disposition :

- Augmenter le niveau de service ou la taille de calcul du pool élastique ou de la base de données. Consultez [Mise à l’échelle des ressources d’une base de données unique](sql-database-single-database-scale.md) et [Mise à l'échelle des ressources d’un pool élastique](sql-database-elastic-pool-scale.md).
- Optimiser les requêtes afin de réduire l’utilisation des ressources de chaque requête si la cause de l’utilisation du travail accrue est un problème de contention des ressources de calcul. Pour plus d’informations, consultez la page [Paramétrage/Compréhension de requêtes](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Gouvernance de taux de journaux de transaction 
Gouvernance de taux de journaux de transaction est un processus dans la base de données SQL Azure permet de limiter le taux d’ingestion élevés pour les charges de travail telles que bulk insert, SELECT INTO, et génère des index. Ces limites sont suivies et appliquées au niveau de seconde pour le taux de génération d’enregistrements journal, limitation de débit, quel que soit le nombre d’IOs peut être émis sur les fichiers de données.  Taux de génération de journaux de transaction actuellement une échelle linéaire jusqu'à un point qui dépendent du matériel, avec le journal maximal taux autorisé est de 96 Mo/s avec le modèle d’achat de Vcores. 

> [!NOTE]
> IOs physiques réels pour les fichiers journaux des transactions ne sont pas régies ou limitées. 

Taux de journal est configurés pour peut être obtenues et maintenus dans un large éventail de scénarios, tandis que l’ensemble du système peut gérer ses fonctionnalités avec un impact réduit la charge utilisateur. Gouvernance de taux de journal garantit ce journal des transactions sauvegardes soient conservées au sein de la récupérabilité publiée contrats SLA.  Cette gouvernance empêche également une file d’attente excessif sur les réplicas secondaires.

Lorsque des enregistrements de journal sont générées, chaque opération est évaluée et évaluée si elle doit être différé afin de maintenir un taux maximal de journal requis (Mo/s par seconde). Les retards ne sont pas ajoutés lorsque les enregistrements de journal sont vidés sur le stockage, au lieu de cela gouvernance de taux de journal est appliqué pendant la génération de taux de journal proprement dit.

La génération de journal réel taux imposées au moment de l’exécution peut également dépendre de mécanismes de commentaires, temporairement en réduisant les taux de journal autorisée pour le système peut se stabiliser. Gestion d’espace de fichier journal, en évitant insuffisant dans les conditions d’espace journal et le groupe de disponibilité des mécanismes de réplication peut diminuer temporairement les limites globales du système. 

Le trafic de mise en forme governor journal taux est présenté par le biais de types d’attente suivants (exposée dans le [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV) :

| Type d’attente | Notes |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitation de la base de données |
| POOL_LOG_RATE_GOVERNOR | Limitation du pool |
| INSTANCE_LOG_RATE_GOVERNOR | Limitation au niveau de l’instance |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Contrôle des commentaires, réplication physique de groupe de disponibilité dans Premium/critiques pour l’entreprise ne pas suivre |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Contrôle des commentaires, la limitation de taux pour éviter une condition d’espace de journal à l’emploi |
|||

Lorsqu’il rencontre une limite de débit de journal qui est entravent l’évolutivité de votre choix, envisagez les options suivantes :
- Monter en puissance vers un niveau supérieur afin d’obtenir le taux de journal 96 Mo/s maximal. 
- Si les données en cours de chargement sont temporaires, par exemple, de mise en attente dans un processus ETL, il peut être chargé dans tempdb (qui est minimale). 
- Pour les scénarios d’analyse, charger dans une table columnstore en cluster couvert. Cela réduit le taux de journal requis en raison de la compression. Cette technique augmente l’utilisation du processeur et s’applique uniquement aux jeux de données qui bénéficient de l’index cluster columnstore. 

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
- Pour plus d’informations sur les DTU et eDTU, consultez [DTU et eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Pour plus d’informations sur les limites de taille de tempdb, consultez [Base de données tempdb dans SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
