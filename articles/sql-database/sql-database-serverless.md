---
title: Azure SQL Database sans serveur (version préliminaire) | Microsoft Docs
description: Cet article décrit le nouveau niveau de calcul sans serveur et la compare à la couche de calcul provisionné existante
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
ms.date: 05/07/2019
ms.openlocfilehash: 2ab8f272fc264f153144803be772d381c1780512
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143265"
---
# <a name="sql-database-serverless-preview"></a>Base de données SQL sans serveur (version préliminaire)

## <a name="what-is-the-serverless-compute-tier"></a>Quelle est la couche de calcul sans serveur

Base de données SQL sans serveur (version préliminaire) est une couche de calcul factures pour la quantité de calcul utilisée par une base de données par seconde. Sans serveur est optimisé pour les bases de données uniques avec des modèles d’utilisation en rafale qui supportent un délai dans le calcul préchauffage après une période d’inactivité de l’utilisation de prix-performances.
En revanche, les offres disponibles publiquement sur facture aujourd'hui de base de données SQL pour la quantité de calculent configurée sur une base horaire. Ce niveau de calcul provisionné est optimisé pour les bases de données uniques ou élastiques prix-performances des pools avec l’utilisation moyenne plus élevée qui ne peut pas se permettre de tout retard dans le préchauffage du calcul.

Une base de données dans le niveau d’ordinateur sans serveur est paramétré par la plage de calcul qu’il peut utiliser et un délai de pause automatique.

![facturation sans serveur](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Performances

- `MinVcore` et `MaxVcore` sont des paramètres configurables qui définissent la plage de capacité de calcul disponible pour la base de données. Limites de mémoire et d’e/s sont proportionnelles à la plage de vCore spécifiée.  
- Le délai de pause automatique est un paramètre configurable qui définit la période de temps que la base de données doit être inactif avant qu’il est suspendu automatiquement. La base de données reprend automatiquement lors de la prochaine connexion se produit.

### <a name="pricing"></a>Tarifs

- La facture totale pour une base de données sans serveur est la somme de la facture de calcul et de la facture de stockage.
Facturation pour le calcul est basée sur la quantité de vCores utilisés et de la mémoire utilisée par seconde.
- Le calcul minimal facturé est basé sur le nombre minimal de VCORE et la mémoire minimum.
- Lorsque la base de données est suspendue, uniquement le stockage est facturé.

## <a name="scenarios"></a>Scénarios

Sans serveur est optimisé pour les bases de données uniques avec des modèles d’utilisation en rafale qui supportent un délai dans le calcul préchauffage après une période d’inactivité de l’utilisation de rapport prix-performances. Le niveau de calcul provisionné est optimisé pour les bases de données uniques ou regroupées avec l’utilisation moyenne plus élevée qui ne peut pas se permettre de tout retard dans le préchauffage de calcul rapport prix-performances.

Le tableau suivant compare le niveau de calcul sans serveur avec le niveau de calcul provisionné :

||Calcul sans serveur|Calcul approvisionné|
|---|---|---|
|**Scénario d’utilisation classique**|Bases de données avec l’utilisation en rafale, imprévisible entrecoupé de périodes d’inactivité|Bases de données ou des pools élastiques avec une utilisation plus régulière.|
|**Effort de gestion des performances**|inférieur|Supérieure|
|**Mise à l’échelle de calcul**|Automatique|Manuel|
|**Réactivité de calcul**|Inférieur après les périodes d’inactivité|Immédiat|
|**Granularité de facturation**|par seconde|Par heure|
|

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scénarios adaptés pour le calcul sans serveur

- Bases de données uniques avec des modèles d’utilisation en rafale intercalés avec des périodes d’inactivité peuvent bénéficier d’économies de prix sont basées sur la facturation par seconde pour la quantité de calcul utilisé.
- Bases de données uniques avec à la demande de ressource qui est difficile à prédire et les clients qui préfèrent pour déléguer le dimensionnement de calcul pour le service.
- Bases de données uniques dans le niveau calcul provisionné modifient fréquemment des niveaux de performances.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scénarios adaptés pour le calcul provisionné

- L’utilisation au fil du temps de calcul de bases de données uniques avec plus normal et les plus importants.
- Bases de données qui ne peut pas tolérer les compromis de performances résultant plus fréquentent mémoire trimming ou différer dans autoresuming à partir d’un état suspendu.
- Plusieurs bases de données avec des modèles d’utilisation en rafale qui peuvent être consolidées en un seul serveur et utilisent des pools élastiques pour une meilleure optimisation des prix.


## <a name="purchasing-model-and-service-tier"></a>Niveau de service et de modèle d’achat

SQL Database sans serveur est actuellement pris en charge uniquement dans le niveau usage général sur du matériel de génération 5 dans le modèle d’achat de vcores.

## <a name="autoscaling"></a>Mise à l’échelle automatique

### <a name="scaling-responsiveness"></a>Mise à l’échelle de temps de réponse

En règle générale, les bases de données sont exécutés sur un ordinateur avec une capacité suffisante pour répondre à la demande de ressources sans interruption pour n’importe quel volume de calcul demandé au sein des limites définies le `maxVcores` valeur. Parfois, automatiquement d’équilibrage de charge se produit si l’ordinateur ne parvient pas à satisfaire la demande de ressource dans quelques minutes. La base de données reste en ligne au cours de l’équilibrage de charge à l’exception d’une courte période à la fin de l’opération lorsque les connexions sont supprimées.

### <a name="memory-management"></a>Gestion de la mémoire

Mémoire pour les bases de données sans serveur est récupérée plus fréquemment que pour les bases de données configurés. Ce comportement est important de contrôler les coûts dans sans serveur. Contrairement au calcul provisionné, mémoire du cache SQL est récupérée à partir d’une base de données sans serveur lors de l’utilisation du processeur ou de cache est faible.

## <a name="autopause-and-autoresume"></a>Pause automatique et autoresume

### <a name="autopause"></a>Pause automatique

Pause automatique est déclenchée si toutes les conditions suivantes sont remplies pour la durée du délai de pause automatique :

- Sessions numéro = 0
- Processeur = 0 (pour les charges de travail utilisateur en cours d’exécution dans le pool de l’utilisateur)

Une option est fournie pour désactiver la pause automatique si vous le souhaitez.

### <a name="autoresume"></a>Autoresume

Autoresume est déclenchée si une des conditions suivantes sont remplie à tout moment :

|Fonctionnalité|Autoresume déclencheur|
|---|---|
|Authentification et autorisation|Connexion|
|Détection de menaces|L’activation/désactivation des paramètres de détection des menaces au niveau de la base de données ou de serveur<br>Modification des paramètres de détection des menaces au niveau de la base de données ou de serveur|
|Découverte et classification des données|Ajout, modification, suppression ou l’affichage des étiquettes de sensibilité|
|Audit|Affichage des enregistrements d’audit.<br>La mise à jour ou la consultation de stratégie d’audit|
|Masquage de données|Ajout, modification, suppression ou l’affichage des règles de masquage des données|
|Chiffrement transparent des données|État d’affichage ou l’état de chiffrement transparent des données|
|Magasin de données de requête (performance)|Modification ou l’affichage des paramètres de magasin de requête ; le réglage automatique|
|Réglage automatique|Application et vérification des recommandations de réglage automatique telles que l’indexation automatique|
|Copie de base de données|Créer la base de données en tant que copie<br>Exporter vers un fichier BACPAC|
|Synchronisation des données SQL|Synchronisation entre les bases de données hub et de membre qui s’exécutent selon une planification configurable ou sont effectuées manuellement|
|Modification de certaines métadonnées de base de données|Ajout de nouvelles balises de base de données<br>Modification du nombre maximal de VCORE, nombre minimal de VCORE, délai de pause automatique|
|SQL Server Management Studio (SSMS)|À l’aide de SSMS version du 18 et l’ouverture d’une nouvelle fenêtre de requête pour une base de données dans le serveur va reprendre une base de données suspendue automatiquement dans le même serveur. Ce comportement n’a pas lieu si à l’aide de SSMS version 17.9.1 avec IntelliSense désactivée.|

### <a name="connectivity"></a>Connectivité

Si une base de données sans serveur est suspendue, la première connexion reprendre la base de données et retourner une erreur indiquant que la base de données n’est pas disponible. Une fois que la reprise de la base de données, la connexion doit être retentée pour établir la connectivité. Clients de base de données avec la logique de nouvelle tentative de connexion ne doivent pas besoin d’être modifiée.

### <a name="latency"></a>Latence

La latence de pause automatique ou autoresume une base de données sans serveur est généralement l’ordre de 1 minute.

### <a name="feature-support"></a>Prise en charge de la fonctionnalité

Les fonctionnalités suivantes ne prennent pas en charge autopausing et autoresuming. Autrement dit, si une des fonctionnalités suivantes sont utilisée, la base de données reste en ligne, quel que soit la durée d’inactivité de la base de données :

- Géo-réplication (active géo-réplication et auto groupes de basculement)
- Rétention des sauvegardes à long terme (LTR)
- La base de données de synchronisation utilisé dans SQL data sync.


## <a name="on-boarding-into-the-serverless-compute-tier"></a>Intégration dans la couche de calcul sans serveur

Création d’une base de données ou déplacement de qu'une base de données existante à un niveau de calcul sans serveur suit le même modèle que la création d’une nouvelle base de données mis en service de niveau de calcul et implique les deux étapes suivantes :

1. Spécifiez le nom d’objectif de service. Le tableau suivant présente le niveau de service disponibles et les tailles de calcul actuellement disponibles dans la version préliminaire publique.

   |Niveau de service|Taille de calcul|
   |---|---|
   |Usage général|GP_S_Gen5_1|
   |Usage général|GP_S_Gen5_2|
   |Usage général|GP_S_Gen5_4|

2. Si vous le souhaitez, spécifiez le délai minimal de vCores et pause automatique pour modifier leurs valeurs par défaut. Le tableau suivant présente les valeurs disponibles pour ces paramètres.

   |Paramètre|Choix de valeur|Valeur par défaut|
   |---|---|---|---|
   |Nombre minimal de vCore|Un des {0,5, 1, 2, 4} ne dépassant ne pas de nombre maximal de VCORE|0,5 VCORE|
   |Autopause delay|Min : 360 minutes (6 heures)<br>Max : 10 080 minutes (7 jours)<br>Incréments : 60 minutes<br>Désactiver la pause automatique : -1|360 minutes|

> [!NOTE]
> À l’aide de T-SQL pour déplacer une base de données existante dans sans serveur ou de modifier sa taille de calcul n’est pas actuellement pris en charge, mais peut être effectuée via le portail Azure ou PowerShell.

### <a name="create-new-database-using-the-azure-portal"></a>Créer la nouvelle base de données à l’aide du portail Azure

Consultez [Démarrage rapide : Créer une base de données dans la base de données SQL Azure à l’aide du portail Azure](sql-database-single-database-get-started.md).

### <a name="create-new-database-using-powershell"></a>Créer la nouvelle base de données à l’aide de PowerShell

L’exemple suivant crée une nouvelle base de données dans la couche de calcul sans serveur définie par l’objectif de service nommé GP_S_Gen5_4 avec les valeurs par défaut pour le délai de vCores et pause automatique min.

Sans serveur nécessite une version plus récente de PowerShell qu’il n’est actuellement dans la galerie, par conséquent, exécutez `Update-Module Az.Sql` pour obtenir les dernières applets de commande sans serveur prenant en charge.

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

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>Déplacer la base de données existante dans la couche de calcul sans serveur

L’exemple suivant déplace une base de données unique à partir de la couche de calcul configurée dans la couche de calcul sans serveur. Cet exemple utilise les valeurs par défaut pour le nombre minimal de VCORE, le nombre maximal de VCORE et le délai de pause automatique.

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

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>Déplacer une base de données hors de la couche de calcul sans serveur

Une base de données sans serveur peut être déplacé dans un niveau de calcul provisionné dans la même façon que le déplacement d’une base de données de calcul provisionné à un niveau de calcul sans serveur.

## <a name="modify-serverless-configuration-parameters"></a>Modifier les paramètres de configuration sans serveur

### <a name="maximum-vcores"></a>Nombre maximal de vCore

Modification du nombre de VCORE maximale est effectuée à l’aide de la [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) commande dans PowerShell à l’aide la `MaxVcore` argument.

### <a name="minimum-vcores"></a>Nombre minimal de vCore

Modification du nombre de VCORE maximale est effectuée à l’aide de la [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) commande dans PowerShell à l’aide la `MinVcore` argument.

### <a name="autopause-delay"></a>Autopause delay

Modification du nombre de VCORE maximale est effectuée à l’aide de la [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) commande dans PowerShell à l’aide la `AutoPauseDelay` argument.

## <a name="monitor-serverless-database"></a>Base de données sans serveur moniteur

### <a name="resources-used-and-billed"></a>Ressources utilisées et facturés

Les ressources d’une base de données sans serveur sont encapsulées par les entités suivantes :

#### <a name="app-package"></a>Package d’application

Le package d’application est une limite de gestion des ressources la plupart des externe pour une base de données, que la base de données soit un niveau de calcul sans serveur ou approvisionné. Le package d’application contient l’instance SQL et des services externes cette étendue ensemble toutes les ressources d’utilisateur et système utilisées par une base de données dans la base de données SQL. R et recherche en texte intégral sont des exemples de services externes. En règle générale, l’instance SQL domine l’utilisation globale des ressources sur le package d’application.

#### <a name="user-resource-pool"></a>Pool de ressources utilisateur

Le pool de ressources utilisateur est interne à la plupart des limite de gestion des ressources pour une base de données, que la base de données soit un niveau de calcul sans serveur ou approvisionné. Le pool de ressources utilisateur portées UC et e/s des charges de travail utilisateur généré par les requêtes DDL (par exemple, CREATE, ALTER, etc.) et DML (par exemple, SELECT, INSERT, UPDATE, DELETE, etc.). Ces requêtes représentent généralement la proportion plus importante de l’utilisation dans le package d’application.

### <a name="metrics"></a>Mesures

|Entité|Métrique|Description|Units|
|---|---|---|---|
|Package d’application|app_cpu_percent|Pourcentage de VCORE utilisés par l’application par rapport à VCORE maximale autorisée pour l’application.|Pourcentage|
|Package d’application|app_cpu_billed|La quantité de calcul facturée pour l’application pendant la période du rapport. Le montant payé pendant cette période est le produit de cette mesure et le prix unitaire de vCore.<br>Valeurs de cette métrique sont déterminées par l’agrégation au fil du temps, le nombre maximal de processeur utilisé et la mémoire utilisée par seconde.<br>Si la quantité utilisée est inférieure à la quantité minimale configurée en tant que jeu par le nombre minimal de VCORE et la mémoire minimum, la quantité minimale configurée est facturée.  Pour comparer des UC avec mémoire pour la facturation, mémoire est normalisée en unités de VCORE par remise à l’échelle de la quantité de mémoire en Go par 3 Go par vCore.|secondes de vCore|
|Package d’application|app_memory_percent|Pourcentage de mémoire utilisée par l’application par rapport à la mémoire maximale autorisée pour l’application.|Pourcentage|
|Pool d’utilisateur|cpu_percent|Pourcentage de VCORE utilisés par charge de travail utilisateur par rapport à VCORE maximale autorisée pour les charges de travail utilisateur.|Pourcentage|
|Pool d’utilisateur|data_IO_percent|Pourcentage de données e/s utilisées par les charges de travail utilisateur par rapport aux e/s de données maximales autorisée pour les charges de travail utilisateur.|Pourcentage|
|Pool d’utilisateur|log_IO_percent|Pourcentage du journal Mo/s utilisées par les charges de travail utilisateur par rapport à max Mo/s du journal autorisée pour les charges de travail utilisateur.|Pourcentage|
|Pool d’utilisateur|workers_percent|Pourcentage de workers utilisé par la charge de travail utilisateur par rapport aux travailleurs maximales autorisée pour les charges de travail utilisateur.|Pourcentage|
|Pool d’utilisateur|sessions_percent|Pourcentage de sessions utilisé par charge de travail utilisateur par rapport à des sessions maximale autorisée pour les charges de travail utilisateur.|Pourcentage|
____

> [!NOTE]
> Mesures dans le portail Azure sont disponibles dans le volet de la base de données pour une base de données sous **surveillance**.

### <a name="pause-and-resume-status"></a>Suspendre et reprendre l’état

Dans le portail Azure, l’état de la base de données est affiché dans le volet Vue d’ensemble du serveur qui répertorie les bases de données qu’il contient. L’état de la base de données s’affiche également dans le volet Vue d’ensemble de la base de données.

Utilisation de la commande PowerShell suivante pour interroger le suspendre et reprendre l’état d’une base de données :

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Limites des ressources

Pour les limites de ressources, consultez [niveau de calcul sans serveur](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>Facturation

La quantité de calcul facturée par seconde est le nombre maximal de processeur utilisée et la mémoire utilisée par seconde. Si la quantité d’UC utilisée et de mémoire utilisée est inférieure à la quantité minimale pour chacune, le montant configuré est facturé. Pour comparer des UC avec mémoire pour la facturation, mémoire est normalisée en unités de VCORE par remise à l’échelle de la quantité de mémoire en Go par 3 Go par vCore.

- **Ressource facturé**: Processeur et mémoire
- **Montant facturé ($)**: vCore UnitPrice * max (nombre minimal de VCORE, vCores utilisés, mémoire min Go * 1/3, mémoire utilisé Go * 1/3) 
- **Fréquence de facturation**: par seconde

La quantité de calcul facturée est exposée par les mesures suivantes :

- **Métrique**: app_cpu_billed (vCore en secondes)
- **Définition**: max (nombre minimal de VCORE, vCores utilisés, mémoire min Go * 1/3, mémoire utilisé Go * 1/3) *
- **Fréquence des rapports**: Par minute

> [!NOTE]
> \* Cette quantité est calculée chaque seconde et agrégée sur 1 minute.

**Exemple**: Considérez une base de données à l’aide de GP_S_Gen5_4 avec l’utilisation suivante sur une période d’une heure :

|Durée (heures : minutes)|app_cpu_billed (vCore en secondes)|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1255|
||Total : 1631|

Supposons que le prix unitaire de calcul est $0.2609/vCore/hour. Le calcul facturé pour cette période d’une heure est déterminée à l’aide de la formule suivante : **$0.2609/vCore/hour * 1631 vCore secondes * 3 600 secondes à 1 heure = $0.1232**

## <a name="available-regions"></a>Régions disponibles

Le niveau de calcul sans serveur est disponible dans toutes les régions à l’exception des régions suivantes : Est de l’Australie centrale, Chine, Nord de la Chine, France-Sud, Nord-est de l’Allemagne centrale, Allemagne, Inde-ouest, Corée du Sud, Afrique du Sud Ouest, Royaume-Uni Nord, Royaume-Uni Sud, Royaume-Uni ouest et centre ouest des États-Unis

## <a name="next-steps"></a>Étapes suivantes

Pour les limites de ressources, consultez [limites de ressources de niveau de calcul Serverless](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).