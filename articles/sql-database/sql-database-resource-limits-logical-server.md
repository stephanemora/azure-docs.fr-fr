---
title: Limites de ressources d’Azure SQL Database – serveur logique | Microsoft Docs
description: Cet article offre un aperçu des limites de ressources d'Azure SQL Database pour les bases de données uniques et regroupées utilisant des pools élastiques. Il fournit également des informations concernant ce qui se passe lorsque ces limites de ressources sont atteintes ou dépassées.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: b48c090cc67d4557140b5734f1a5e1f763b271ab
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829561"
---
# <a name="sql-database-resource-limits-for-single-and-pooled-databases-on-a-logical-server"></a>Limites de ressources de SQL Database pour les bases de données uniques et regroupées sur un serveur logique 

Cet article offre un aperçu des limites de ressources de SQL Database pour les bases de données uniques et regroupées sur un serveur logique. Il fournit également des informations concernant ce qui se passe lorsque ces limites de ressources sont atteintes ou dépassées.

> [!NOTE]
> Pour les limites de Managed Instance, consultez [Limites de ressources de SQL Database pour les instances gérées](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites maximales de ressources

| Ressource | Limite |
| :--- | :--- |
| Bases de données par serveur | 5 000 |
| Nombre par défaut de serveurs par abonnement dans n’importe quelle région | 20 |
| Nombre maximal de serveurs par abonnement dans n’importe quelle région | 200 |  
| Quota DTU/eDTU par serveur | 54 000 |  
| Quota vCore par serveur/instance | 540 |
| Nombre maximal de pools par serveur | Limité par le nombre de DTU ou de vCores |
||||

> [!NOTE]
> Pour augmenter le quota DTU/eDTU ou vCore ou pour obtenir une quantité de serveurs supérieure à la quantité par défaut, vous pouvez envoyer une nouvelle demande de support dans le Portail Azure pour l’abonnement en indiquant le type de problème « Quota ». Le quota DTU/eDTU et le nombre maximal de bases de données par serveur limitent le nombre de pools élastiques par serveur. 

> [!IMPORTANT]
> Le nombre de bases de données approchant la limite par serveur logique, ce qui suit peut se produire :
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
- Réduire une base de données afin de récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

### <a name="sessions-and-workers-requests"></a>Sessions et workers (requêtes) 

Le nombre maximal de sessions et de workers est déterminé par le niveau de service et la taille de calcul (DTU et eDTU). Lorsque les limites de sessions ou de workers sont atteintes, les nouvelles requêtes sont refusées et les clients reçoivent un message d’erreur. Si le nombre de connexions disponibles peut être contrôlé par l’application, le nombre de workers simultanés est souvent plus difficile à estimer et à contrôler. Cela est particulièrement vrai pendant les pics de charge lorsque les limites de ressources de base de données sont atteintes et que les workers s’accumulent en raison de requêtes plus longues à exécuter. 

En cas d’utilisation élevée de workers ou de sessions, voici certaines des options d’atténuation à votre disposition :
- Augmenter le niveau de service ou la taille de calcul du pool élastique ou de la base de données. Consultez [Mise à l’échelle des ressources d’une base de données unique](sql-database-single-database-scale.md) et [Mise à l'échelle des ressources d’un pool élastique](sql-database-elastic-pool-scale.md).
- Optimiser les requêtes afin de réduire l’utilisation des ressources de chaque requête si la cause de l’utilisation du travail accrue est un problème de contention des ressources de calcul. Pour plus d’informations, consultez la page [Paramétrage/Compréhension de requêtes](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Étapes suivantes

- Consultez [SQL Database FAQ](sql-database-faq.md) (FAQ de SQL Database) pour obtenir des réponses aux questions fréquemment posées.
- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
- Pour plus d’informations sur les DTU et eDTU, consultez [DTU et eDTU](sql-database-service-tiers.md#dtu-based-purchasing-model).
- Pour plus d’informations sur les limites de taille de tempdb, consultez [Base de données tempdb dans SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
