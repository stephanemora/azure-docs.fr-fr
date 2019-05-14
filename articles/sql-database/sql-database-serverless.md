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
ms.date: 05/11/2019
ms.openlocfilehash: ba79e2b9552f0c27ac11501b2b125a126e40eb1d
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551637"
---
# <a name="sql-database-serverless-preview"></a>SQL Database serverless (préversion)

## <a name="what-is-the-serverless-compute-tier"></a>Qu’est-ce que le niveau de calcul serverless ?

SQL Database serverless (préversion) est un niveau de calcul qui facture le volume de calcul utilisé par une base de données unique par seconde. Serverless constitue un rapport prix/performance optimisé pour les bases de données uniques avec des modèles d’utilisation intermittents imprévisibles pouvant supporter un délai dans le préchauffage du calcul après des périodes d’inactivité.

Une base de données dans le niveau de calcul serverless est paramétrée par la plage de calcul qu’elle peut utiliser et un délai de mise en pause automatique.

![facturation de serverless](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Performances

- `MinVcore` et `MaxVcore` sont des paramètres configurables qui définissent la plage de la capacité de calcul disponible pour la base de données. Les limites en mémoire et E/S sont proportionnelles à la plage de vCore spécifiée.  
- Le délai de mise en pause automatique est un paramètre configurable qui définit la durée pendant laquelle la base de données doit être inactive avant d’être automatiquement mise en pause. La base de données reprend automatiquement lors de la prochaine connexion.

### <a name="pricing"></a>Tarifs

- Le total de la facture d’une base de données serverless correspond à la somme du calcul facturé et du stockage facturé.
La facturation du calcul est basée sur la quantité de vCores utilisés et de la mémoire utilisée par seconde.
- Le calcul minimal facturé est basé sur un nombre minimal de vCores et une mémoire minimum.
- Lorsque la base de données est en pause, seul le stockage est facturé.

## <a name="scenarios"></a>Scénarios

Serverless constitue un rapport prix/performance optimisé pour les bases de données uniques avec des modèles d’utilisation intermittents imprévisibles pouvant supporter un délai dans le préchauffage du calcul après des périodes d’inactivité. Par opposition, le niveau de calcul provisionné est un rapport prix/performance optimisé pour les bases de données uniques ou mises en pool avec une utilisation moyenne plus élevée qui ne peuvent pas accepter de délai lors du préchauffage du calcul.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scénarios adaptés au calcul serverless

- Les bases de données uniques avec des modèles d’utilisation intermittents et imprévisibles entrecoupées de périodes d’inactivité pouvant bénéficier d’un prix réduit basé sur une facturation par seconde du volume de calcul utilisé.
- Les bases de données uniques avec des besoins en ressources difficiles à prévoir et des clients qui préfèrent déléguer le volume du calcul au service.
- Les bases de données uniques dans le niveau calcul provisionné qui changent souvent de niveaux de performances.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scénarios adaptés au calcul provisionné

- Les bases de données uniques avec une utilisation du calcul plus uniforme et plus importante dans le temps.
- Les bases de données qui ne peuvent pas tolérer de compromis sur les performances résultant d’insuffisances de mémoire plus fréquentes ou de délais dans la reprise automatique après un état de mise en pause.
- Plusieurs bases de données avec des modèles d’utilisation intermittents et imprévisibles qui peuvent être regroupées dans un seul serveur et qui utilisent des pools élastiques pour une meilleure optimisation des prix.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparaison avec le niveau de calcul provisionné

Le tableau suivant résume les différences entre le niveau de calcul serverless et le niveau de calcul provisionné :

| | **Calcul serverless** | **Calcul provisionné** |
|:---|:---|:---|
|**Principal scénario d’utilisation**| Les bases de données avec une utilisation intermittente imprévisible entrecoupée de périodes d’inactivité. | Les bases de données ou les pools élastiques avec une utilisation plus régulière.|
| **Effort pour gérer les performances** : |Moins grand|Plus grand|
|**Mise à l’échelle du calcul**|Automatique|Manuel|
|**Réactivité du calcul**.|Moins rapide après les périodes d’inactivité|Immédiat|
|**Mode de facturation**|À la seconde|À l’heure|

## <a name="purchasing-model-and-service-tier"></a>Modèle d’achat et niveau de service

Actuellement, SQL Database serverless est juste pris en charge dans le niveau Usage général sur du matériel de génération 5 dans le modèle d’achat Vcore.

## <a name="autoscaling"></a>Mise à l’échelle automatique

### <a name="scaling-responsiveness"></a>Réactivité de la mise à l’échelle

En règle générale, les bases de données sont exécutées sur un ordinateur avec une capacité suffisante pour répondre aux besoins en ressources sans interruption, quel que soit le volume de calcul demandé dans les limites définies par la valeur `maxVcores`. Parfois, un équilibrage de charge se produit automatiquement si l’ordinateur ne parvient pas à satisfaire les besoins en ressources en quelques minutes. La base de données reste en ligne au cours de l’équilibrage de charge, sauf pendant une courte période à la fin de l’opération lorsque les connexions sont supprimées.

### <a name="memory-management"></a>Gestion de la mémoire

La mémoire pour les bases de données serverless est sollicitée plus fréquemment que pour les bases de données provisionnées. Ce comportement est important pour maîtriser les coûts dans le niveau serverless. Contrairement au calcul provisionné, la mémoire du cache SQL est sollicitée par une base de données serverless quand l’utilisation du processeur ou du cache est faible.

## <a name="autopause-and-autoresume"></a>Mise en pause automatique et reprise automatique

### <a name="autopause"></a>Mise en pause automatique

Une mise en pause automatique est déclenchée si toutes les conditions suivantes sont remplies pendant la durée du délai de la mise en pause automatique :

- Nombre de sessions = 0
- Processeur = 0 (pour les charges de travail de l’utilisateur exécutées dans le pool de l’utilisateur)

Une option est fournie pour désactiver la mise en pause automatique si vous le souhaitez.

### <a name="autoresume"></a>Reprise automatique

La reprise automatique est déclenchée si l’une des conditions suivantes est remplie à tout moment :

|Fonctionnalité|Déclencheur de reprise automatique|
|---|---|
|Authentification et autorisation|Connexion|
|Détection de menaces|Activation/désactivation des paramètres de détection des menaces au niveau de la base de données ou du serveur<br>Modification des paramètres de détection des menaces au niveau de la base de données ou du serveur|
|Découverte et classification des données|Ajout, modification, suppression ou affichage des étiquettes de sensibilité|
|Audit|Affichage des enregistrements d’audit.<br>Mise à jour ou affichage de la stratégie d’audit|
|Masquage de données|Ajout, modification, suppression ou affichage des règles de masquage des données|
|Chiffrement transparent des données|État ou statut d’affichage du chiffrement transparent des données|
|Magasin de données des requêtes (performances)|Modification ou affichage des paramètres du Magasin des requêtes ; réglage automatique|
|Réglage automatique|Application et vérification des recommandations de réglage automatique telles que l’indexation automatique|
|Copie de base de données|Création d’une base de données comme copie<br>Exportation vers un fichier BACPAC|
|SQL Data Sync|Synchronisation entre les bases de données hub et membre qui s’exécutent selon une planification configurable ou qui sont exécutées manuellement|
|Modification de certaines métadonnées de base de données|Ajout de nouvelles balises de base de données<br>Changement du nombre maximal et minimal de vCores et du délai de la mise pause automatique|
|SQL Server Management Studio (SSMS)|L’utilisation de SSMS version 18 et l’ouverture d’une nouvelle fenêtre de requête pour toute base de données du serveur reprennent les bases de données du même serveur qui ont été mises en pause automatiquement. Ce comportement ne se produit pas si vous utilisez SSMS version 17.9.1 avec IntelliSense désactivé.|

### <a name="connectivity"></a>Connectivité

Si une base de données serverless est mise en pause, la première connexion reprend la base de données et retourne une erreur indiquant que la base de données n’est pas disponible avec le code d’erreur 40613. Une fois que la base de données est reprise, la connexion doit être retentée pour établir la connectivité. Les clients de base de données avec une logique de nouvelle tentative de connexion n’ont normalement pas besoin d’être modifiés.

### <a name="latency"></a>Latence

La latence pour la mise en pause automatique ou la reprise automatique d’une base de données serverless est généralement de l’ordre de 1 minute.

### <a name="feature-support"></a>Prise en charge des fonctionnalités

Les fonctionnalités suivantes ne prennent pas en charge la mise en pause automatique et la reprise automatique. Autrement dit, si l’une des fonctionnalités suivantes est utilisée, la base de données reste en ligne quelle que soit sa durée d’inactivité :

- Géoréplication (géoréplication active et groupes de basculement automatique)
- Conservation de sauvegardes à long terme (LTR)
- Base de données de synchronisation utilisée dans SQL Data Sync.


## <a name="on-boarding-into-the-serverless-compute-tier"></a>Intégration au niveau de calcul serverless

La création d’une base de données ou le déplacement d’une base de données existante dans un niveau de calcul serverless suivent le même modèle que la création d’une nouvelle base de données dans un niveau de calcul provisionné et impliquent les deux étapes suivantes :

1. Spécifier le nom de l’objectif de service. L’objectif de service précise le niveau de service, la génération du matériel et le nombre maximal de vCores. Le tableau suivant présente les options d’objectif de service :

   |Nom de l’objectif de service|Niveau de service|Génération du matériel|Nombre maximal de vCores|
   |---|---|---|---|
   |GP_S_Gen5_1|Usage général|Gen5|1|
   |GP_S_Gen5_2|Usage général|Gen5|2|
   |GP_S_Gen5_4|Usage général|Gen5|4|

2. Si vous le souhaitez, spécifiez le nombre minimal de vCores et le délai de mise en pause automatique pour changer leurs valeurs par défaut. Le tableau suivant présente les valeurs disponibles pour ces paramètres.

   |Paramètre|Choix des valeurs|Valeur par défaut|
   |---|---|---|---|
   |Nombre minimal de vCores|Au choix, {0,5, 1, 2, 4} ne dépassant pas le nombre maximal de vCores|0,5 vCore|
   |Délai de la mise en pause automatique|Min : 360 minutes (6 heures)<br>Max : 10 080 minutes (7 jours)<br>Incréments : 60 minutes<br>Désactiver la mise en pause automatique  -1|360 minutes|

> [!NOTE]
> Actuellement, l’utilisation de T-SQL pour déplacer une base de données existante dans le niveau serverless ou changer sa taille de calcul n’est pas prise en charge, mais vous pouvez effectuer ces opérations via le portail Azure ou PowerShell.

### <a name="create-new-database-using-the-azure-portal"></a>Créer une base de données à l’aide du portail Azure

Consultez [Démarrage rapide : Créez une base de données unique dans Azure SQL Database à l’aide du portail Azure](sql-database-single-database-get-started.md).

### <a name="create-new-database-using-powershell"></a>Créer une base de données à l’aide de PowerShell

L’exemple suivant crée une base de données dans le niveau de calcul serverless défini par l’objectif de service nommé GP_S_Gen5_4 avec les valeurs par défaut pour le nombre minimal de vCores et le délai de mise en pause automatique.

Serverless nécessite une version plus récente de PowerShell que celle actuellement dans la galerie, donc exécutez `Update-Module Az.Sql` pour obtenir les dernières applets de commande prenant en charge le niveau serverless.

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
  -AutoPauseDelay 720
```

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>Déplacer une base de données existante dans le niveau de calcul serverless

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
  -AutoPauseDelay 1440
```

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>Enlever une base de données du niveau de calcul serverless

Vous pouvez déplacer une base de données serverless dans un niveau de calcul provisionné de la même façon que lorsque vous déplacez une base de données de calcul provisionné dans un niveau de calcul serverless.

## <a name="modify-serverless-configuration-parameters"></a>Modifier les paramètres de configuration serverless

### <a name="maximum-vcores"></a>Nombre maximal de vCores

Pour modifier le nombre maximal de vCores, utilisez la commande [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) dans PowerShell à l’aide de l’argument `MaxVcore`.

### <a name="minimum-vcores"></a>Nombre minimal de vCores

Pour modifier le nombre minimal de vCores, utilisez la commande [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) dans PowerShell à l’aide de l’argument `MinVcore`.

### <a name="autopause-delay"></a>Délai de la mise en pause automatique

Pour modifier le délai de mise en pause automatique, utilisez la commande [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) dans PowerShell à l’aide de l’argument `AutoPauseDelay`.

## <a name="monitor-serverless-database"></a>Superviser une base de données serverless

### <a name="resources-used-and-billed"></a>Ressources utilisées et facturées

Les ressources d’une base de données serverless sont encapsulées par les entités suivantes :

#### <a name="app-package"></a>Package d’application

Le package d’application représente le maximum en termes de gestion des ressources d’une base de données, que celle-ci soit dans un niveau de calcul serverless ou provisionné. Le package d’application contient l’instance SQL et les services externes qui comprennent ensemble toutes les ressources utilisateur et système utilisées par une base de données dans SQL Database. Comme services externes, citons par exemple R et la recherche en texte intégral. En règle générale, l’instance SQL domine l’utilisation globale des ressources sur le package d’application.

#### <a name="user-resource-pool"></a>Pool de ressources utilisateur

Le pool de ressources utilisateur représente le minimum en termes de gestion des ressources d’une base de données, que celle-ci soit dans un niveau de calcul serverless ou provisionné. Le pool de ressources utilisateur comprend le processeur et les E/S de la charge de travail utilisateur générée par les requêtes DDL (par exemple, CREATE, ALTER, etc.) et les requêtes DML (par exemple, SELECT, INSERT, UPDATE, DELETE, etc.). Ces requêtes représentent généralement la proportion la plus importante de l’utilisation dans le package d’application.

### <a name="metrics"></a>Mesures

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
- **Montant facturé ($)**  : prix unitaire d’un vCore * max (vCores min, vCores utilisés, mémoire min Go * 1/3, mémoire Go utilisée * 1/3) 
- **Fréquence de facturation** : À la seconde

Prix unitaire d’un vCore dans le coût par vCore par seconde. Reportez-vous à la [page des prix Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) pour connaître les prix à l’unité d’une région donnée.

Le volume de calcul facturé est exposé par les métriques suivantes :

- **Métrique** : app_cpu_billed (secondes de vCore)
- **Définition** : max (vCores min, vCores utilisés, mémoire min Go * 1/3, mémoire Go utilisée * 1/3)
- **Fréquence de reporting** : Par minute

Cette quantité est calculée chaque seconde et agrégée sur 1 minute.

**Exemple**: Prenez une base de données utilisant GP_S_Gen5_4 avec l’utilisation suivante sur une période d’une heure :

|Durée (heures:minutes)|app_cpu_billed (secondes de vCore)|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1 255|
||Total : 1 631|

Supposons que le prix unitaire du calcul est $0.000073/vCore/seconde. Le calcul facturé pour cette période d’une heure est déterminé en utilisant la formule suivante : **$0.000073/vCore/seconde * 1631 secondes de vCore = $0.1191**

## <a name="available-regions"></a>Régions disponibles

Le niveau de calcul serverless est disponible dans toutes les régions, sauf dans les régions suivantes : Australie Centre, Chine Est, Chine Nord, France Sud, Allemagne Centre, Allemagne Nord-Est, Inde Ouest, Corée Sud, Afrique du Sud Ouest, Royaume-Uni Nord, Royaume-Uni Sud, Royaume-Uni Ouest et USA Centre-Ouest

## <a name="next-steps"></a>Étapes suivantes

- Pour démarrer, consultez [Démarrage rapide : Créez une base de données unique dans Azure SQL Database à l’aide du portail Azure](sql-database-single-database-get-started.md).
- Pour les limites de ressources, consultez [Limites des ressources du niveau de calcul serverless](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)