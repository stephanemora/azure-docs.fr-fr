---
title: Sans serveur
description: Cet article décrit le nouveau niveau de calcul serverless et le compare au niveau de calcul provisionné existant
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: d1f3bf6cb1467d0bb4906ff2409e72828b22cd20
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807015"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database serverless

Azure SQL Database serverless est un niveau de calcul pour les bases de données uniques qui met automatiquement à l’échelle les calculs en fonction de la demande de charge de travail, et facture la quantité de calcul utilisée par seconde. Le niveau de calcul serverless met aussi automatiquement en pause les bases de données pendant les périodes d’inactivité, quand seul le stockage est facturé, et reprend leur exécution automatiquement avec l’activité.

## <a name="serverless-compute-tier"></a>Niveau de calcul serverless

Le niveau de calcul serverless pour une base de données unique est paramétré par une plage de mise à l’échelle automatique du calcul et un délai de mise en pause automatique.  La configuration de ces paramètres déterminent l’expérience de performances de base de données et le coût du calcul.

![facturation de serverless](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Configuration des performances

- Les quantités **minimale** et **maximale** de vCores sont des paramètres configurables qui définissent la plage de la capacité de calcul disponible pour la base de données. Les limites en mémoire et E/S sont proportionnelles à la plage de vCore spécifiée.  
- Le **délai de mise en pause automatique** est un paramètre configurable qui définit la durée pendant laquelle la base de données doit être inactive avant d’être automatiquement mise en pause. La base de données reprend automatiquement lors de la prochaine connexion ou activité.  Vous pouvez également désactiver la mise en pause automatique.

### <a name="cost"></a>Coût

- Le coût d’une base de données serverless correspond à la somme du coût de calcul et du coût de stockage.
- Quand l’utilisation du calcul est comprise dans les limites minimales et maximales configurées, le coût du calcul est basé sur le nombre de vCores et la mémoire utilisée.
- Quand l’utilisation du calcul est inférieure à la limite minimale configurée, le coût du calcul est basé sur le nombre minimal de vCores et la mémoire minimale configurée.
- Quand la base de données est en pause, le coût du calcul est égal à zéro et seuls les coûts de stockage sont facturés.
- Le coût de stockage est déterminé de la même façon que dans le niveau de calcul provisionné.

Pour plus de détails sur les coûts, consultez [Facturation](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scénarios

Serverless constitue un rapport prix/performance optimisé pour les bases de données uniques avec des modèles d’utilisation intermittents imprévisibles pouvant supporter un délai dans le préchauffage du calcul après des périodes d’inactivité. Par opposition, le niveau de calcul provisionné présente un rapport prix/performance optimisé pour une base de données unique ou plusieurs bases de données dans des pools élastiques avec une utilisation moyenne plus élevée qui ne tolèrent aucun délai lors du préchauffage du calcul.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scénarios adaptés au calcul serverless

- Les bases de données uniques avec des modèles d’utilisation intermittente et imprévisible entrecoupée de périodes d’inactivité, et une utilisation du calcul moyenne moins élevée dans le temps.
- Les bases de données uniques dans le niveau de calcul provisionné qui sont souvent mises à l’échelle et les clients qui préfèrent déléguer la mise à l’échelle du calcul au service.
- Les nouvelles bases de données uniques sans historique d’utilisation où la taille de calcul est difficile ou impossible à estimer avant le déploiement dans SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scénarios adaptés au calcul provisionné

- Les bases de données uniques avec des modèles d’utilisation plus régulière et prévisible, et une utilisation du calcul moyenne plus élevée dans le temps.
- Les bases de données qui ne peuvent pas tolérer de compromis sur les performances résultant d’insuffisances de mémoire plus fréquentes ou de délais dans la reprise automatique après un état de mise en pause.
- Plusieurs bases de données avec des modèles d’utilisation intermittente et imprévisible qui peuvent être regroupées dans des pools élastiques pour un rapport prix/performances optimisé.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparaison avec le niveau de calcul provisionné

Le tableau suivant résume les différences entre le niveau de calcul serverless et le niveau de calcul provisionné :

| | **Calcul serverless** | **Calcul provisionné** |
|:---|:---|:---|
|**Modèle d’utilisation des bases de données**| Utilisation intermittente et imprévisible avec une utilisation moyenne du calcul moins élevée dans le temps. |  Modèles d’utilisation plus régulière avec une utilisation moyenne du calcul plus élevée dans le temps, ou plusieurs bases de données regroupées dans des pools élastiques.|
| **Effort pour gérer les performances** : |Moins grand|Plus grand|
|**Mise à l’échelle du calcul**|Automatique|Manuel|
|**Réactivité du calcul**.|Moins rapide après les périodes d’inactivité|Immédiat|
|**Mode de facturation**|À la seconde|À l’heure|

## <a name="purchasing-model-and-service-tier"></a>Modèle d’achat et niveau de service

Actuellement, SQL Database serverless est juste pris en charge dans le niveau Usage général sur du matériel de génération 5 dans le modèle d’achat Vcore.

## <a name="autoscaling"></a>Mise à l’échelle automatique

### <a name="scaling-responsiveness"></a>Réactivité de la mise à l’échelle

En règle générale, les bases de données serverless sont exécutées sur une machine d’une capacité suffisante pour répondre aux besoins en ressources sans interruption, quel que soit le volume de calcul demandé dans les limites définies par le nombre maximal de vCores. Parfois, un équilibrage de charge se produit automatiquement si l’ordinateur ne parvient pas à satisfaire les besoins en ressources en quelques minutes. Par exemple, si la demande de ressources est de quatre vCores, mais qu’il y a seulement deux vCores disponibles, un délai de quelques minutes peut être nécessaire pour équilibrer la charge avant de fournir les quatre vCores demandés. La base de données reste en ligne au cours de l’équilibrage de charge, sauf pendant une courte période à la fin de l’opération lorsque les connexions sont supprimées.

### <a name="memory-management"></a>Gestion de la mémoire

La mémoire pour les bases de données serverless est davantage sollicitée que pour les bases de données de calcul provisionné. Il est important de tenir compte de ce comportement pour maîtriser les coûts dans le niveau serverless et déterminer l’impact sur les performances.

#### <a name="cache-reclamation"></a>Récupération du cache

Contrairement aux bases de données de calcul provisionné, la mémoire du cache SQL est sollicitée par une base de données serverless quand l’utilisation du processeur ou du cache est faible.

- L’utilisation du cache est considérée comme faible quand la taille totale des dernières entrées du cache utilisées tombe sous un certain seuil pendant une période donnée.
- Quand la récupération du cache est déclenchée, la taille de cache cible est réduite de façon incrémentielle à une fraction de sa taille précédente, et la récupération continue uniquement si l’utilisation reste faible.
- S’il y a une récupération du cache, la stratégie de sélection des entrées du cache à supprimer est la même que celle utilisée pour les bases de données de calcul provisionné quand la mémoire est fortement sollicitée.
- La taille du cache n’est jamais réduite en deçà de la limite de mémoire minimale telle que définie par le nombre minimal de vCores configuré, le cas échéant.

Dans les bases de données de niveaux de calcul serverless et provisionné, des entrées de cache peuvent être supprimées si toute la mémoire disponible est utilisée.

#### <a name="cache-hydration"></a>Alimentation du cache

La taille du cache SQL augmente à mesure que des données sont extraites du disque, de la même manière et au même rythme que pour des bases de données provisionnées. Quand la base de données est occupée, le cache est autorisé à grossir sans contrainte jusqu’à la limite de mémoire maximale.

## <a name="autopausing-and-autoresuming"></a>Mise en pause automatique et reprise automatique

### <a name="autopausing"></a>Mise en pause automatique

Une mise en pause automatique est déclenchée si toutes les conditions suivantes sont remplies pendant la durée du délai de mise en pause automatique :

- Nombre de sessions = 0
- Processeur = 0 pour la charge de travail utilisateur exécutée dans le pool d’utilisateurs

Une option permet de désactiver la mise en pause automatique si vous le souhaitez.

Les fonctionnalités suivantes ne prennent pas en charge la mise en pause automatique.  Autrement dit, si l’une des fonctionnalités suivantes est utilisée, la base de données reste en ligne quelle que soit sa durée d’inactivité :

- Géoréplication (géoréplication active et groupes de basculement automatique).
- Conservation de sauvegardes à long terme (LTR).
- Base de données de synchronisation utilisée dans SQL Data Sync.  Contrairement aux bases de données de synchronisation, les bases de données de hub et de membres prennent en charge la mise en pause automatique.
- Base de données de travail utilisée dans les travaux élastiques.

La mise en pause automatique est temporairement indisponible durant le déploiement de certaines mises à jour de service pour lesquelles la base de données doit être en ligne.  Dans ce cas, la mise en pause automatique est réactivée dès que la mise à jour du service est terminée.

### <a name="autoresuming"></a>Reprise automatique

La reprise automatique est déclenchée si l’une des conditions suivantes est remplie à un moment donné :

|Fonctionnalité|Déclencheur de reprise automatique|
|---|---|
|Authentification et autorisation|Connexion|
|Détection de menaces|Activation/désactivation des paramètres de détection des menaces au niveau de la base de données ou du serveur.<br>Modification des paramètres de détection des menaces au niveau de la base de données ou du serveur.|
|Découverte et classification des données|Ajout, modification, suppression ou affichage des étiquettes de sensibilité|
|Audit|Affichage des enregistrements d’audit.<br>Mise à jour ou affichage de la stratégie d’audit.|
|Masquage de données|Ajout, modification, suppression ou affichage des règles de masquage des données|
|Chiffrement transparent des données|État ou statut d’affichage du chiffrement transparent des données|
|Magasin de données des requêtes (performances)|Modification ou affichage des paramètres du magasin des requêtes|
|Réglage automatique|Application et vérification des recommandations de réglage automatique telles que l’indexation automatique|
|Copie de base de données|Création d’une base de données comme copie.<br>Exportation vers un fichier BACPAC.|
|SQL Data Sync|Synchronisation entre les bases de données hub et membre qui s’exécutent selon une planification configurable ou qui sont exécutées manuellement|
|Modification de certaines métadonnées de base de données|Ajout de nouvelles balises de base de données.<br>Changement du nombre maximal de vCores, du nombre minimal de vCores ou du délai de mise en pause automatique.|
|SQL Server Management Studio (SSMS)|L’utilisation de versions de SSMS antérieures à 18.1 et l’ouverture d’une nouvelle fenêtre de requête pour toute base de données du serveur entraînent la reprise des bases de données du même serveur qui ont été mises en pause automatiquement. Ce comportement ne se produit pas si vous utilisez SSMS version 18.1 ou ultérieure.|

La reprise automatique est également déclenchée durant le déploiement de certaines mises à jour de service pour lesquelles la base de données doit être en ligne.

### <a name="connectivity"></a>Connectivité

Si une base de données serverless est mise en pause, la première connexion reprend la base de données et retourne une erreur indiquant que la base de données n’est pas disponible (code d’erreur 40613). Une fois que la base de données est reprise, la connexion doit être retentée pour établir la connectivité. Les clients de base de données avec une logique de nouvelle tentative de connexion n’ont normalement pas besoin d’être modifiés.

### <a name="latency"></a>Latence

La latence pour la reprise automatique d’une base de données serverless est généralement d’environ une minute et pour la mise en pause automatique, entre une et dix minutes.

## <a name="onboarding-into-serverless-compute-tier"></a>Intégration au niveau de calcul serverless

La création d’une base de données ou le déplacement d’une base de données existante dans un niveau de calcul serverless s’effectuent selon le même modèle que la création d’une base de données dans un niveau de calcul provisionné, et impliquent les deux étapes suivantes.

1. Spécifier le nom de l’objectif de service. L’objectif de service précise le niveau de service, la génération du matériel et le nombre maximal de vCores. Le tableau suivant présente les options d’objectif de service :

   |Nom de l’objectif de service|Niveau de service|Génération du matériel|Nombre maximal de vCores|
   |---|---|---|---|
   |GP_S_Gen5_1|Usage général|Gen5|1|
   |GP_S_Gen5_2|Usage général|Gen5|2|
   |GP_S_Gen5_4|Usage général|Gen5|4|
   |GP_S_Gen5_6|Usage général|Gen5|6|
   |GP_S_Gen5_8|Usage général|Gen5|8|
   |GP_S_Gen5_10|Usage général|Gen5|10|
   |GP_S_Gen5_12|Usage général|Gen5|12|
   |GP_S_Gen5_14|Usage général|Gen5|14|
   |GP_S_Gen5_16|Usage général|Gen5|16|

2. Si vous le souhaitez, spécifiez un nombre minimal de vCores et un délai de mise en pause automatique différents des valeurs par défaut. Le tableau suivant présente les valeurs disponibles pour ces paramètres.

   |Paramètre|Choix des valeurs|Valeur par défaut|
   |---|---|---|---|
   |Nombre minimal de vCores|Dépend du nombre maximal de vCores configuré. Voir [Limites des ressources](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 vCore|
   |Délai de la mise en pause automatique|Minimum : 60 minutes (1 heure)<br>Maximum : 10 080 minutes (7 jours)<br>Incréments : 60 minutes<br>Désactiver la mise en pause automatique  -1|60 minutes|


### <a name="create-new-database-in-serverless-compute-tier"></a>Créer une base de données dans le niveau de calcul serverless 

#### <a name="use-azure-portal"></a>Utiliser le portail Azure

Consultez [Démarrage rapide : Créez une base de données unique dans Azure SQL Database à l’aide du portail Azure](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>Utiliser PowerShell

L’exemple suivant crée une base de données dans le niveau de calcul serverless.  Cet exemple spécifie explicitement le nombre minimal de vCores, le nombre maximal de vCores et le délai de mise en pause automatique.

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

#### <a name="use-transact-sql-t-sql"></a>Utiliser Transact-SQL (T-SQL)

L’exemple suivant crée une base de données dans le niveau de calcul serverless.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Pour plus d’informations, consultez [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Déplacer une base de données du niveau de calcul provisionné vers le niveau de calcul serverless

#### <a name="use-powershell"></a>Utiliser PowerShell

L’exemple suivant déplace une base de données du niveau de calcul provisionné vers le niveau de calcul serverless. Cet exemple spécifie explicitement le nombre minimal de vCores, le nombre maximal de vCores et le délai de mise en pause automatique.

```powershell
Set-AzSqlDatabase `
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

#### <a name="use-transact-sql-t-sql"></a>Utiliser Transact-SQL (T-SQL)

L’exemple suivant déplace une base de données du niveau de calcul provisionné vers le niveau de calcul serverless. 

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Pour plus d’informations, consultez [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Déplacer une base de données du niveau de calcul serverless vers le niveau de calcul provisionné

Vous pouvez déplacer une base de données serverless dans un niveau de calcul provisionné de la même façon que lorsque vous déplacez une base de données de calcul provisionné dans un niveau de calcul serverless.

## <a name="modifying-serverless-configuration"></a>Modifier la configuration serverless

### <a name="maximum-vcores"></a>Nombre maximal de vCores

#### <a name="use-powershell"></a>Utiliser PowerShell

Pour modifier le nombre maximal de vCores, utilisez la commande [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) dans PowerShell avec l’argument `MaxVcore`.

### <a name="minimum-vcores"></a>Nombre minimal de vCores

#### <a name="use-powershell"></a>Utiliser PowerShell

Pour modifier le nombre minimal de vCores, utilisez la commande [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) dans PowerShell avec l’argument `MinVcore`.

### <a name="autopause-delay"></a>Délai de la mise en pause automatique

#### <a name="use-powershell"></a>Utiliser PowerShell

Pour modifier le délai de mise en pause automatique, utilisez la commande [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) dans PowerShell avec l’argument `AutoPauseDelayInMinutes`.

## <a name="monitoring"></a>Surveillance

### <a name="resources-used-and-billed"></a>Ressources utilisées et facturées

Les ressources d’une base de données serverless sont encapsulées par le package d’application, l’instance SQL et les entités du pool de ressources utilisateur.

#### <a name="app-package"></a>Package d’application

Le package d’application représente le maximum en termes de gestion des ressources d’une base de données, que celle-ci soit dans un niveau de calcul serverless ou provisionné. Le package d’application contient l’instance SQL et les services externes qui comprennent ensemble toutes les ressources utilisateur et système utilisées par une base de données dans SQL Database. Comme services externes, citons par exemple R et la recherche en texte intégral. En règle générale, l’instance SQL domine l’utilisation globale des ressources sur le package d’application.

#### <a name="user-resource-pool"></a>Pool de ressources utilisateur

Le pool de ressources utilisateur représente le minimum en termes de gestion des ressources d’une base de données, que celle-ci soit dans un niveau de calcul serverless ou provisionné. Le pool de ressources utilisateur comprend le processeur et les E/S de la charge de travail utilisateur générée par les requêtes DDL comme CREATE et ALTER, et par les requêtes DML comme SELECT, INSERT, UPDATE et DELETE. Ces requêtes représentent généralement la proportion la plus importante de l’utilisation dans le package d’application.

### <a name="metrics"></a>Mesures

Les métriques de supervision de l’utilisation des ressources du package d’application et du pool d’utilisateurs d’une base de données serverless sont listées dans le tableau suivant :

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

Pour connaître les limites de ressources, consultez [Niveau de calcul serverless](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Facturation

Le volume de calcul facturé correspond à la quantité maximale de processeur et de mémoire utilisée par seconde. Si la quantité de processeur et de mémoire utilisée est inférieure à la quantité minimale provisionnée pour chaque, la quantité provisionnée est facturée. Pour comparer le processeur à la mémoire à des fins de facturation, la mémoire est normalisée en unités de vCores en remettant à l’échelle la quantité de mémoire en Go à 3 Go par vCore.

- **Ressource facturée** : Processeur et mémoire
- **Montant facturé**  : prix unitaire d’un vCore * max (vCores min, vCores utilisés, mémoire min Go * 1/3, mémoire Go utilisée * 1/3) 
- **Fréquence de facturation** : À la seconde

Prix unitaire d’un vCore est le coût par vCore par seconde. Reportez-vous à la [page des prix Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) pour connaître les prix à l’unité d’une région donnée.

Le volume de calcul facturé est exposé par les métriques suivantes :

- **Métrique** : app_cpu_billed (secondes de vCore)
- **Définition** : max (vCores min, vCores utilisés, mémoire min Go * 1/3, mémoire Go utilisée * 1/3)
- **Fréquence de reporting** : Par minute

Cette quantité est calculée chaque seconde et agrégée sur 1 minute.

Prenons l’exemple d’une base de données serverless configurée avec 1 vCore min et 4 vCores max.  Cela représente environ 3 Go de mémoire min et 12 Go de mémoire max.  Supposons que le délai de mise en pause automatique est défini à six heures et que la charge de travail de la base de données est active durant les deux premières heures d’une période de 24 heures, mais qu’elle reste inactive le reste du temps.    

Dans ce cas de figure, la base de données est facturée pour le calcul et le stockage pendant les huit premières heures.  Même si la base de données devient inactive après la deuxième heure, elle continue d’être facturée pour le calcul durant les six heures suivantes, selon le calcul minimal provisionné quand la base de données est en ligne.  Seul le stockage est facturé pendant le reste de la période de 24 heures où la base de données est mise en pause.

Plus précisément, les coûts de calcul dans cet exemple sont calculés de la façon suivante :

|Intervalle de temps|vCores utilisés par seconde|Go utilisés par seconde|Dimension de calcul facturée|Secondes de vCore facturées dans l’intervalle de temps|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores utilisés|4 vCores * 3 600 secondes = 14 400 secondes de vCore|
|1:00-2:00|1|12|Mémoire utilisée|12 GB * 1/3 * 3 600 secondes = 14 400 secondes de vCore|
|2:00-8:00|0|0|Mémoire minimale provisionnée|3 GB * 1/3 * 21 600 secondes = 21 600 secondes de vCore|
|8:00-24:00|0|0|Aucun calcul facturé pendant la mise en pause|0 seconde de vCore|
|Total de secondes de vCore facturées sur 24 heures||||50 400 secondes de vCore|

Supposons que le prix unitaire du calcul est $0.000073/vCore/seconde.  Le calcul facturé sur cette période de 24 heures est le produit du prix unitaire du calcul par le nombre de secondes de vCore facturées : $0.000073/vCore/seconde * 50400 secondes de vCore = $3.68

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hybrid Benefit et capacité réservée

Les remises liées à Azure Hybrid Benefit et aux capacités réservées ne s’appliquent pas au niveau du calcul serverless.

## <a name="available-regions"></a>Régions disponibles

Le niveau de calcul serverless est disponible partout dans le monde, sauf dans les régions suivantes : Chine Est, Chine Nord, Allemagne Centre, Allemagne Nord-Est, Royaume-Uni Nord, Royaume-Uni Sud 2, USA Centre-Ouest et USA Centre-Ouest, US DoD et US Gov Centre (Iowa).

## <a name="next-steps"></a>Étapes suivantes

- Pour démarrer, consultez [Démarrage rapide : Créez une base de données unique dans Azure SQL Database à l’aide du portail Azure](sql-database-single-database-get-started.md).
- Pour les limites de ressources, consultez [Limites des ressources du niveau de calcul serverless](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)
