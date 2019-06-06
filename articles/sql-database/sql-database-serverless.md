---
title: Azure SQL Database serverless (préversion) | Microsoft Docs
description: Cet article décrit le nouveau niveau de calcul serverless et le compare au niveau de calcul provisionné existant
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 06/05/2019
ms.openlocfilehash: b39d2c839444e3cad60d5ff08e117282ecc04d7a
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734777"
---
# <a name="sql-database-serverless-preview"></a>SQL Database serverless (préversion)

## <a name="serverless-compute-tier"></a>Niveau de calcul serverless

Base de données SQL sans serveur (version préliminaire) est un niveau de calcul de base de données unique qu’opère de calcul et que la quantité de calcul utilisée par seconde. 

Une base de données dans le niveau de calcul serverless est paramétrée par la plage de calcul qu’elle peut utiliser et un délai de mise en pause automatique.

![facturation de serverless](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Performances

- Le nombre de vCores minimale et maximale VCORE est des paramètres configurables qui définissent la plage de capacité de calcul disponible pour la base de données. Les limites en mémoire et E/S sont proportionnelles à la plage de vCore spécifiée.  
- Le délai de mise en pause automatique est un paramètre configurable qui définit la durée pendant laquelle la base de données doit être inactive avant d’être automatiquement mise en pause. La base de données reprend automatiquement lors de la prochaine connexion.

### <a name="pricing"></a>Tarifs

- Le total de la facture d’une base de données serverless correspond à la somme du calcul facturé et du stockage facturé.
La facturation du calcul est basée sur la quantité de vCores utilisés et de la mémoire utilisée par seconde.
- Le calcul minimal facturé est basé sur un nombre minimal de vCores et une mémoire minimum.
- Lorsque la base de données est en pause, seul le stockage est facturé.

## <a name="scenarios"></a>Scénarios

Serverless constitue un rapport prix/performance optimisé pour les bases de données uniques avec des modèles d’utilisation intermittents imprévisibles pouvant supporter un délai dans le préchauffage du calcul après des périodes d’inactivité. En revanche, le niveau de calcul provisionné est optimisé pour les bases de données uniques ou plusieurs bases de données dans des pools élastiques et l’utilisation moyenne plus élevée qui ne peut pas se permettre de tout retard dans le préchauffage de calcul rapport prix-performances.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scénarios adaptés au calcul serverless

- Bases de données uniques avec des modèles d’utilisation intermittents, imprévisible intercalé avec des périodes d’inactivité et d’utilisation de calcul moyen inférieur au fil du temps.
- Bases de données uniques dans la couche de calcul provisionné fréquemment sont remises à l’échelle et les clients qui préfèrent déléguer remise à l’échelle pour le service de calcul.
- Nouvelles bases de données uniques sans l’historique d’utilisation où le dimensionnement de calcul est difficile ou impossible à estimer avant le déploiement dans la base de données SQL.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scénarios adaptés au calcul provisionné

- L’utilisation au fil du temps de calcul de bases de données uniques avec des modèles d’utilisation plus régulière et prévisible et moyenne plus élevée.
- Les bases de données qui ne peuvent pas tolérer de compromis sur les performances résultant d’insuffisances de mémoire plus fréquentes ou de délais dans la reprise automatique après un état de mise en pause.
- Plusieurs bases de données avec des modèles d’utilisation intermittents et imprévisibles qui peuvent être consolidées dans des pools élastiques pour une meilleure optimisation de prix-performances.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparaison avec le niveau de calcul provisionné

Le tableau suivant résume les différences entre le niveau de calcul serverless et le niveau de calcul provisionné :

| | **Calcul serverless** | **Calcul provisionné** |
|:---|:---|:---|
|**Modèle d’utilisation de base de données**| Utilisation par intermittence, imprévisible avec une utilisation moyenne de calcul inférieur au fil du temps. |  L’utilisation au fil du temps, ou plusieurs bases de données à l’aide de pools élastiques de calcul de modèles d’utilisation plus régulières avec moyenne supérieure.|
| **Effort pour gérer les performances** : |Moins grand|Plus grand|
|**Mise à l’échelle du calcul**|Automatique|Manuel|
|**Réactivité du calcul**.|Moins rapide après les périodes d’inactivité|Immédiat|
|**Mode de facturation**|À la seconde|À l’heure|

## <a name="purchasing-model-and-service-tier"></a>Modèle d’achat et niveau de service

Actuellement, SQL Database serverless est juste pris en charge dans le niveau Usage général sur du matériel de génération 5 dans le modèle d’achat Vcore.

## <a name="autoscale"></a>Autoscale

### <a name="scaling-responsiveness"></a>Réactivité de la mise à l’échelle

En règle générale, les bases de données sans serveur sont exécutés sur un ordinateur avec une capacité suffisante pour répondre à la demande de ressources sans interruption pour n’importe quel volume de calcul demandé au sein des limites définies par la valeur du nombre maximal de VCORE. Parfois, un équilibrage de charge se produit automatiquement si l’ordinateur ne parvient pas à satisfaire les besoins en ressources en quelques minutes. Par exemple, si la demande de ressource est 4 vCores, mais seulement 2 vCores sont disponibles, puis il peut falloir quelques minutes pour équilibrer la charge avant de 4 vCores sont fournies. La base de données reste en ligne au cours de l’équilibrage de charge, sauf pendant une courte période à la fin de l’opération lorsque les connexions sont supprimées.

### <a name="memory-management"></a>Gestion de la mémoire

Mémoire pour les bases de données sans serveur est récupérée plus fréquemment que pour les bases de données de calcul provisionné. Ce comportement est important de contrôler les coûts dans sans serveur et peut affecter les performances.

#### <a name="cache-reclamation"></a>Récupération du cache

Contrairement aux bases de données de calcul provisionné, mémoire du cache SQL est récupérée à partir d’une base de données sans serveur lors de l’utilisation du processeur ou de cache est faible.

- Utilisation du cache est considérée comme faible lorsque la taille totale du dernier récemment utilisé cache entrées tombe sous un seuil pour une période donnée.
- Lors de la récupération du cache est déclenchée, la taille de cache cible est réduite de façon incrémentielle à une fraction de sa taille d’origine et la récupération continue uniquement si l’utilisation reste faible.
- En cas de récupération du cache, la stratégie de sélection des entrées du cache à supprimer est la stratégie de sélection de même que pour les bases de données de calcul provisionné lors de la sollicitation de la mémoire est élevée.
- La taille du cache n’est jamais inférieure de la limite de mémoire min tel que défini par le nombre minimal de VCORE qui peuvent être configurés.

Dans sans serveur et mis en service de calcul bases de données, cache entrées peuvent être supprimées si toute la mémoire disponible est utilisée.

#### <a name="cache-hydration"></a>Mettre en cache d’hydratation

Le cache SQL augmente à mesure que les données sont extraites à partir du disque de la même manière et avec la même vitesse que pour les bases de données configurés. Lorsque la base de données est occupé, le cache est autorisé à croître sans contrainte jusqu'à la limite de mémoire maximale.

## <a name="autopause-and-autoresume"></a>Mise en pause automatique et reprise automatique

### <a name="autopause"></a>Mise en pause automatique

Autopausing est déclenchée si toutes les conditions suivantes sont remplies pour la durée du délai de pause automatique :

- Nombre de sessions = 0
- Processeur = 0 pour les charges de travail utilisateur en cours d’exécution dans le pool de l’utilisateur

Une option est fournie pour désactiver autopausing si vous le souhaitez.

Les fonctionnalités suivantes ne prennent pas en charge autopausing.  Autrement dit, si une des fonctionnalités suivantes sont utilisée, la base de données reste en ligne, quel que soit la durée d’inactivité de la base de données :

- Géo-réplication (groupes de géo-réplication et basculement automatique actives).
- Rétention des sauvegardes à long terme (LTR).
- Base de données de synchronisation utilisée dans SQL Data Sync.

Autopausing ne peut pas temporairement pendant le déploiement de certaines mises à jour de service qui requièrent que la base de données soit en ligne.  Dans ce cas, autopausing devient autorisé à nouveau une fois la mise à jour du service terminée.

### <a name="autoresume"></a>Reprise automatique

Autoresuming est déclenchée si une des conditions suivantes sont remplie à tout moment :

|Fonctionnalité|Déclencheur de reprise automatique|
|---|---|
|Authentification et autorisation|Connexion|
|Détection de menaces|L’activation/désactivation des paramètres de détection des menaces au niveau de la base de données ou serveur.<br>Modification des paramètres de détection des menaces au niveau de la base de données ou serveur.|
|Découverte et classification des données|Ajout, modification, suppression ou affichage des étiquettes de sensibilité|
|Audit|Affichage des enregistrements d’audit.<br>La mise à jour ou la consultation de stratégie d’audit.|
|Masquage de données|Ajout, modification, suppression ou affichage des règles de masquage des données|
|Chiffrement transparent des données|État ou statut d’affichage du chiffrement transparent des données|
|Magasin de données des requêtes (performances)|Modification ou affichage des paramètres du Magasin des requêtes ; réglage automatique|
|Réglage automatique|Application et vérification des recommandations de réglage automatique telles que l’indexation automatique|
|Copie de base de données|Créer la base de données en tant que copie.<br>Exporter vers un fichier BACPAC.|
|SQL Data Sync|Synchronisation entre les bases de données hub et membre qui s’exécutent selon une planification configurable ou qui sont exécutées manuellement|
|Modification de certaines métadonnées de base de données|Ajout de nouvelles balises de base de données.<br>Modifier le nombre maximal de VCORE, nombre minimal de VCORE ou de retard de pause automatique.|
|SQL Server Management Studio (SSMS)|L’utilisation de SSMS version 18 et l’ouverture d’une nouvelle fenêtre de requête pour toute base de données du serveur reprennent les bases de données du même serveur qui ont été mises en pause automatiquement. Ce comportement ne se produit pas si vous utilisez SSMS version 17.9.1 avec IntelliSense désactivé.|

Autoresuming est également déclenché lors du déploiement de certaines mises à jour de service qui requièrent que la base de données soit en ligne.

### <a name="connectivity"></a>Connectivité

Si une base de données sans serveur est suspendue, la première connexion reprendre la base de données et retourner une erreur indiquant que la base de données n’est pas disponible avec le code d’erreur 40613. Une fois que la base de données est reprise, la connexion doit être retentée pour établir la connectivité. Les clients de base de données avec une logique de nouvelle tentative de connexion n’ont normalement pas besoin d’être modifiés.

### <a name="latency"></a>Latence

La latence à autoresume et pause automatique une base de données sans serveur est généralement l’ordre de 1 minute à autoresume et 1 à 10 minutes de pause automatique.

## <a name="onboarding-into-serverless-compute-tier"></a>Intégration dans le niveau de calcul sans serveur

La création d’une base de données ou le déplacement d’une base de données existante dans un niveau de calcul serverless suivent le même modèle que la création d’une nouvelle base de données dans un niveau de calcul provisionné et impliquent les deux étapes suivantes :

1. Spécifier le nom de l’objectif de service. L’objectif de service prescrit le niveau de service, la génération du matériel et le nombre maximal de VCORE. Le tableau suivant présente les options d’objectif de service :

   |Nom de l’objectif de service|Niveau de service|Génération du matériel|Nombre maximal de vCores|
   |---|---|---|---|
   |GP_S_Gen5_1|Usage général|Gen5|1|
   |GP_S_Gen5_2|Usage général|Gen5|2|
   |GP_S_Gen5_4|Usage général|Gen5|4|

2. Si vous le souhaitez, spécifiez le délai minimal vCores et pause automatique avant pour modifier leurs valeurs par défaut. Le tableau suivant présente les valeurs disponibles pour ces paramètres.

   |Paramètre|Choix des valeurs|Valeur par défaut|
   |---|---|---|---|
   |Nombre minimal de VCORE|Au choix, {0,5, 1, 2, 4} ne dépassant pas le nombre maximal de vCores|0,5 vCore|
   |Délai de la mise en pause automatique|Min : 360 minutes (6 heures)<br>Max : 10 080 minutes (7 jours)<br>Incréments : 60 minutes<br>Désactiver la mise en pause automatique  -1|360 minutes|

> [!NOTE]
> Actuellement, l’utilisation de T-SQL pour déplacer une base de données existante dans le niveau serverless ou changer sa taille de calcul n’est pas prise en charge, mais vous pouvez effectuer ces opérations via le portail Azure ou PowerShell.

### <a name="create-new-serverless-database-using-azure-portal"></a>Créer la nouvelle base de données sans serveur à l’aide du portail Azure

Consultez [Démarrage rapide : Créez une base de données unique dans Azure SQL Database à l’aide du portail Azure](sql-database-single-database-get-started.md).

### <a name="create-new-serverless-database-using-powershell"></a>Créer la nouvelle base de données sans serveur à l’aide de PowerShell

L’exemple suivant crée une base de données dans le niveau de calcul serverless défini par l’objectif de service nommé GP_S_Gen5_4 avec les valeurs par défaut pour le nombre minimal de vCores et le délai de mise en pause automatique.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelayInMinutes 720
```

### <a name="move-provisioned-compute-database-into-serverless-compute-tier"></a>Déplacer la base de données de calcul provisionné dans le niveau de calcul sans serveur

L’exemple suivant déplace une base de données unique existante du niveau de calcul provisionné vers le niveau de calcul serverless. Cet exemple spécifie explicitement le nombre minimal de vCores, le nombre maximal de vCores et le délai de mise en pause automatique.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-serverless-database-into-provisioned-compute-tier"></a>Déplacer la base de données sans serveur dans la couche de calcul provisionné

Vous pouvez déplacer une base de données serverless dans un niveau de calcul provisionné de la même façon que lorsque vous déplacez une base de données de calcul provisionné dans un niveau de calcul serverless.

## <a name="modifying-serverless-configuration"></a>Modification de la configuration sans serveur

### <a name="maximum-vcores"></a>Nombre maximal de vCores

Modifier le nombre maximal de VCORE est effectuée à l’aide de la [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) commande dans PowerShell à l’aide la `MaxVcore` argument.

### <a name="minimum-vcores"></a>Nombre minimal de vCores

Modifier le nombre minimal de VCORE est effectuée à l’aide de la [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) commande dans PowerShell à l’aide la `MinVcore` argument.

### <a name="autopause-delay"></a>Délai de la mise en pause automatique

Modifier le délai de pause automatique est effectuée à l’aide de la [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) commande dans PowerShell à l’aide la `AutoPauseDelayInMinutes` argument.

## <a name="monitoring"></a>Surveillance

### <a name="resources-used-and-billed"></a>Ressources utilisées et facturées

Les ressources d’une base de données serverless sont encapsulées par les entités suivantes :

#### <a name="app-package"></a>Package d’application

Le package d’application représente le maximum en termes de gestion des ressources d’une base de données, que celle-ci soit dans un niveau de calcul serverless ou provisionné. Le package d’application contient l’instance SQL et les services externes qui comprennent ensemble toutes les ressources utilisateur et système utilisées par une base de données dans SQL Database. Comme services externes, citons par exemple R et la recherche en texte intégral. En règle générale, l’instance SQL domine l’utilisation globale des ressources sur le package d’application.

#### <a name="user-resource-pool"></a>Pool de ressources utilisateur

Le pool de ressources utilisateur représente le minimum en termes de gestion des ressources d’une base de données, que celle-ci soit dans un niveau de calcul serverless ou provisionné. L’utilisateur pool étendues de ressources processeur et e/s pour les charges de travail utilisateur générées par les requêtes DDL telles que les requêtes CREATE et ALTER et DML telles que sélectionner, insérer, mettre à jour et supprimer. Ces requêtes représentent généralement la proportion la plus importante de l’utilisation dans le package d’application.

### <a name="metrics"></a>metrics

|Entité|Métrique|Description|Units|
|---|---|---|---|
|Package d’application|app_cpu_percent|Pourcentage de vCores utilisés par l’application par rapport au nombre maximal de vCores autorisé pour l’application.|Pourcentage|
|Package d’application|app_cpu_billed|Volume de calcul facturé pour l’application pendant la période de rapport. Le montant payé pendant cette période est le produit de cette métrique et du prix unitaire d’un vCore. <br><br>Les valeurs de cette métrique sont déterminées par l’agrégation de la quantité maximale du processeur utilisé et de la mémoire utilisée par seconde. Si la quantité utilisée est inférieure à la quantité minimale provisionnée tel que définie par le nombre minimal de vCores et la mémoire minimum, la quantité minimale provisionnée est facturée. Pour comparer le processeur à la mémoire à des fins de facturation, la mémoire est normalisée en unités de vCores en remettant à l’échelle la quantité de mémoire en Go à 3 Go par vCore.|Secondes de vCore|
|Package d’application|app_memory_percent|Pourcentage de mémoire utilisée par l’application par rapport à la mémoire maximale autorisée pour l’application.|Pourcentage|
|Pool utilisateur|cpu_percent|Pourcentage de vCores utilisés par la charge de travail utilisateur par rapport au nombre maximal de vCores autorisé pour la charge de travail utilisateur.|Pourcentage|
|Pool utilisateur|data_IO_percent|Pourcentage d’IOPS de données utilisées par la charge de travail utilisateur par rapport au nombre maximal d’IOPS de données autorisé pour la charge de travail utilisateur.|Pourcentage|
|Pool utilisateur|log_IO_percent|Pourcentage de Mo/s de journal utilisés par la charge de travail utilisateur par rapport au nombre maximal de Mo/s de journal autorisé pour la charge de travail utilisateur.|Pourcentage|
|Pool utilisateur|workers_percent|Pourcentage de workers utilisés par la charge de travail utilisateur par rapport au nombre maximal de workers autorisé pour la charge de travail utilisateur.|Pourcentage|
|Pool utilisateur|sessions_percent|Pourcentage de session utilisées par la charge de travail utilisateur par rapport au nombre maximal de sessions autorisé pour la charge de travail utilisateur.|Pourcentage|
____

> [!NOTE]
> Les métriques du portail Azure sont disponibles dans le volet de la base de données unique sous **Supervision**.

### <a name="pause-and-resume-status"></a>État de mise en pause et de reprise

Dans le portail Azure, l’état de la base de données est affiché dans le volet Vue d’ensemble du serveur qui liste les bases de données qu’il contient. L’état de la base de données est également affiché dans le volet Vue d’ensemble de la base de données.

Utilisation de la commande PowerShell suivante pour interroger l’état de mise en pause et de reprise d’une base de données :

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Limites des ressources

Pour les limites de ressources, consultez [Niveau de calcul serverless](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>Facturation

Le volume de calcul facturé correspond à la quantité maximale de processeur et de mémoire utilisée par seconde. Si la quantité de processeur et de mémoire utilisée est inférieure à la quantité minimale provisionnée pour chaque, la quantité provisionnée est facturée. Pour comparer le processeur à la mémoire à des fins de facturation, la mémoire est normalisée en unités de vCores en remettant à l’échelle la quantité de mémoire en Go à 3 Go par vCore.

- **Ressource facturée** : Processeur et mémoire
- **Montant facturé**: vCore UnitPrice * max (nombre minimal de VCORE, vCores utilisés, mémoire min Go * 1/3, mémoire utilisé Go * 1/3) 
- **Fréquence de facturation** : À la seconde

Le prix unitaire de vCore dans le coût par vCore par seconde. Reportez-vous à la [page des prix Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) pour connaître les prix à l’unité d’une région donnée.

Le volume de calcul facturé est exposé par les métriques suivantes :

- **Métrique** : app_cpu_billed (secondes de vCore)
- **Définition** : max (vCores min, vCores utilisés, mémoire min Go * 1/3, mémoire Go utilisée * 1/3)
- **Fréquence de reporting** : Par minute

Cette quantité est calculée chaque seconde et agrégée sur 1 minute.

Considérons une base de données sans serveur configuré avec 1 min vCore et le nombre maximal de 4 VCORE.  Cela correspond à environ 3 Go de mémoire min et max 12 Go de mémoire.  Supposons que le délai de pause automatique est défini sur 6 heures et la charge de travail de base de données est active pendant les 2 premières heures d’une période de 24 heures et inactives dans le cas contraire.    

Dans ce cas, la base de données est facturé pour le calcul et de stockage pendant 8 heures des première.  Même si la base de données est inactive ne démarre après la deuxième heure, il est toujours facturé pour le calcul dans les 6 heures suivantes, selon le calcul minimal approvisionné lorsque la base de données est en ligne.  Seul le stockage est facturé pendant le reste de la période de 24 heures pendant que la base de données est suspendu.

Plus précisément, la facture de calcul dans cet exemple est calculée comme suit :

|Intervalle de temps|nombre de VCORE utilisés par seconde|Go utilisés par seconde|Dimension facturée de calcul|secondes de vCore facturées sur l’intervalle de temps|
|---|---|---|---|---|
|0:00-1:00|4|9|nombre de VCORE utilisés|4 vCores * 3600 seconds = 14400 vCore seconds|
|1:00-2:00|1|12|Mémoire utilisée|12 Go * 1/3 * 3 600 secondes = 14400 vCore secondes|
|2:00-8:00|0|0|Provisionnement en mémoire min|3 GB * 1/3 * 21600 seconds = 21600 vCore seconds|
|8:00-24:00|0|0|Aucun calcul facturé pendant la suspension|vCore 0 secondes|
|Secondes de vCore total facturés plus de 24 heures||||vCore 50400 secondes|

Supposons que le prix unitaire du calcul est $0.000073/vCore/seconde.  Le calcul facturé pour cette période de 24 heures est le produit des calcul unit price et vCore secondes facturé : $0.000073/vCore/second * 50400 vCore secondes = $3.68

## <a name="available-regions"></a>Régions disponibles

Le niveau de calcul sans serveur est disponible dans le monde entier, sauf les régions suivantes : Est de l’Australie centrale, Chine, Nord de la Chine, France-Sud, Nord-est de l’Allemagne centrale, Allemagne, Inde-ouest, Corée du Sud, Afrique du Sud Ouest, Royaume-Uni Nord, Royaume-Uni Sud, Royaume-Uni ouest et centre ouest des États-Unis.

## <a name="next-steps"></a>Étapes suivantes

- Pour démarrer, consultez [Démarrage rapide : Créez une base de données unique dans Azure SQL Database à l’aide du portail Azure](sql-database-single-database-get-started.md).
- Pour les limites de ressources, consultez [Limites des ressources du niveau de calcul serverless](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)
