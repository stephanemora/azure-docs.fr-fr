---
title: Migrer de DTU vers vCore
description: Découvrez comment effectuer une migration du modèle DTU vers le modèle vCore. La migration vers vCore s’apparente à la mise à niveau ou au passage à une version antérieure entre les niveaux Standard et Premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945399"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Effectuer une migration à partir du modèle DTU vers le modèle vCore

## <a name="migrate-a-database"></a>Migrer une base de données

La migration d’une base de données du modèle d’achat DTU vers le modèle d’achat vCore est similaire à la mise à niveau ou au passage à une version antérieure entre les niveaux de service standard et premium dans le modèle d’achat DTU.

## <a name="migrate-databases-that-use-geo-replication"></a>Effectuer une migration de bases de données utilisant des liens de géoréplication

La migration du modèle DTU vers le modèle vCore est similaire à la mise à niveau (ou à la rétrogradation) des relations de géoréplication entre les bases de données dans les niveaux de service standard et premium. Pendant la migration, vous n’êtes pas obligé d’arrêter la géoréplication, mais vous devez suivre ces règles de séquencement :

- Lors d’une mise à niveau, vous devez mettre à niveau la base de données secondaire, avant de mettre à niveau la base de données primaire.
- Lors d’une rétrogradation, inversez l’ordre : rétrogradez d’abord la base de données primaire, puis la base de données secondaire.

Lorsque vous utilisez la géoréplication entre deux pools élastiques, nous vous recommandons de désigner un pool comme le pool principal et l’autre comme le pool secondaire. Dans ce cas, lorsque vous effectuez une migration de pools élastiques, vous devez utiliser les mêmes recommandations de séquencement. Toutefois, si vous avez des pools élastiques qui contiennent des bases de données primaires et secondaire, traitez le pool le plus utilisé comme pool principal et suivez les règles de séquencement en conséquence.  

Le tableau suivant fournit des conseils pour certains scénarios de migration :

|Niveau de service actuel|Niveau de service cible|Type de migration|Actions utilisateur|
|---|---|---|---|
|standard|Usage général|Latéral|Peut effectuer la migration dans n’importe quel ordre, mais doit garantir un redimensionnement vCore adapté*|
|Premium|Critique pour l'entreprise|Latéral|Peut effectuer la migration dans n’importe quel ordre, mais doit garantir un redimensionnement vCore adapté*|
|standard|Critique pour l'entreprise|Mettre à niveau|Doit d’abord effectuer la migration de la base de données secondaire|
|Critique pour l'entreprise|standard|Rétrogradation|Doit d’abord effectuer la migration de la base de données primaire|
|Premium|Usage général|Rétrogradation|Doit d’abord effectuer la migration de la base de données primaire|
|Usage général|Premium|Mettre à niveau|Doit d’abord effectuer la migration de la base de données secondaire|
|Critique pour l'entreprise|Usage général|Rétrogradation|Doit d’abord effectuer la migration de la base de données primaire|
|Usage général|Critique pour l'entreprise|Mettre à niveau|Doit d’abord effectuer la migration de la base de données secondaire|
||||

\* En règle générale, chaque ensemble de 100 unités de transaction de base de données du niveau standard nécessite au moins 1 vCore, et chaque ensemble de 125 DTU du niveau premium nécessite au moins 1 vCore. Pour plus d’informations, voir [Modèle d’achat par vCore](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>Migrer des groupes de basculement

La migration des groupes de basculement comprenant plusieurs bases de données nécessite que la base de données primaire et la base de données secondaire soient migrées séparément. Pendant ce processus, les mêmes recommandations et règles de séquencement s’appliquent. Une fois les bases de données converties au modèle d’achat vCore, le groupe de basculement reste actif, avec les mêmes paramètres de stratégie.

### <a name="create-a-geo-replication-secondary-database"></a>Créer une base de données secondaire de géoréplication

Vous pouvez créer une base de données secondaire géoréplication (réplica géosecondaire) uniquement dans le même niveau de service que celui utilisé pour la base de données primaire. Pour les bases de données avec un taux élevé de génération de journaux, nous vous recommandons de créer le réplica géosecondaire avec la même taille de calcul que le réplica principal.

Si vous créez une base de données secondaire de géoréplication dans le pool élastique pour une base de données primaire unique, le paramètre `maxVCore` du pool doit correspondre à la taille de calcul de la base de données primaire. Si vous créez une base de données secondaire de géoréplication pour une base de données primaire située dans un autre pool élastique, nous vous recommandons d’attribuer la même valeur au paramètre `maxVCore` des deux pools.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Utiliser la copie de base de données pour convertir une base de données DTU en base de données vCore

Vous pouvez copier n’importe quelle base de données avec une taille de calcul DTU vers une base de données avec une taille de calcul vCore, sans aucune restriction ni séquencement spécial, tant que la taille de calcul cible prend en charge la taille maximale de la base de données source. La copie de base de données crée un instantané des données dès que commence l’opération de copie, et elle ne synchrone pas les données entre la source et la cible.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tailles de calcul et les tailles de stockage disponibles pour les bases de données uniques, consultez [Limites des ressources vCore SQL Database pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md).
- Pour plus d’informations sur les tailles de calcul et les tailles de stockage disponibles pour les pools élastiques, consultez [Limites des ressources vCore SQL Database pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md).
