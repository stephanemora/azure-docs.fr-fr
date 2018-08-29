---
title: Niveaux de service Azure SQL Database - DTU | Microsoft Docs
description: Découvrez les niveaux de service des bases de données uniques du pool qui permettent de fournir divers niveaux de performance et diverses tailles de stockage.
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: d0250d508ca6d21ee09c9402e10d2fdb025529ac
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42142901"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>Choisir un niveau de service, un niveau de performance et des ressources de stockage basés sur des unités DTU 

Chaque niveau de service a son propre niveau de performance, ainsi qu’une quantité fixe de stockage inclus, une période de rétention fixe pour les sauvegardes et un prix fixe. Tous les niveaux de service permettent de changer de niveau de performance sans nécessiter de temps d’arrêt. Les bases de données et les pools élastiques sont facturés en fonction du niveau de service et du niveau de performance.

> [!IMPORTANT]
> SQL Database Managed Instance, qui est actuellement en préversion publique, ne prend pas en charge le modèle d’achat DTU. Pour plus d’informations, consultez [Azure SQL Database Managed Instance](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>Choisir un niveau de service basé sur des unités DTU

Le choix d’un niveau de service dépend principalement des exigences de continuité d’activité, de stockage et de performance.
||De base|standard|Premium|
| :-- | --: |--:| --:| --:| 
|Charge de travail cible|Développement et production|Développement et production|Développement et production||
|Contrat SLA de durée de fonctionnement|99,99 %|99,99 %|99,99 %|N/A pendant la version préliminaire|
|Rétention des sauvegardes|7 jours|35 jours|35 jours|
|UC|Faible|Faible, moyen, élevé|Faible, élevé|
|Débit d’E/S (approximatif) |2,5 IOPS par DTU| 2,5 IOPS par DTU | 48 IOPS par DTU|
|Latence d’E/S (approximative)|5 ms (lecture), 10 ms (écriture)|5 ms (lecture), 10 ms (écriture)|2 ms (lecture/écriture)|
|Indexation Columnstore |N/A|S3 et supérieur|Pris en charge|
|OLTP en mémoire|N/A|N/A|Pris en charge|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Limites de stockage et unités DTU d’une base de données unique

Les niveaux de performance en termes d’unités de transaction de base de données (DTU) pour des bases de données uniques et d’unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour en savoir plus sur les DTU et les eDTU, consultez [Définition des DTU et des eDTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).

||De base|standard|Premium|
| :-- | --: | --: | --: | --: |
| Taille de stockage maximale | 2 Go | 1 To | 4 To  | 
| DTU maximales | 5. | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>eDTU de pool élastique, stockage et limites de base de données mise en pool

| | **De base** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Taille de stockage maximale par base de données  | 2 Go | 1 To | 1 To | 
| Taille de stockage maximale par pool | 156 Go | 4 To | 4 To | 
| Nombre maximal d’eDTU par base de données | 5. | 3000 | 4000 | 
| eDTU maximales par pool | 1 600 | 3000 | 4000 | 
| Nombre maximal de bases de données par pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Le niveau Premium offre plus de 1 To de stockage dans toutes les régions à l’exception des suivantes : USA Centre-Ouest, Chine Est, USDoDCentral, Allemagne - Centre, USDoDEast, US Gov Sud-Ouest, US Gov Iowa, Allemagne - Nord-Est, Chine Nord. Dans les autres régions, l’espace de stockage maximal au niveau Premium est limité à 1 To. Consultez [Limitations actuelles P11-P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>Test d’évaluation DTU

Les caractéristiques physiques (processeur, mémoire, E/S) associées à chaque mesure DTU sont étalonnées à l’aide d’un test d’évaluation qui simule une charge de travail de base de données réelle.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Mise en corrélation des résultats du test d’évaluation avec les performances réelles de la base de données
Il est important de comprendre que tous les tests d’évaluation fournissent uniquement des résultats représentatifs et indicatifs. Les taux de transaction obtenus avec l’application d’évaluation ne seront pas les mêmes que ceux qui peuvent être atteints avec d’autres applications. Le test d’évaluation comprend un ensemble de différents types de transactions qui s’exécutent sur un schéma contenant une plage de tables et de types de données. Si le test d’évaluation exécute les mêmes opérations de base communes à toutes les charges de travail OLTP, il n’a pas vocation à représenter une classe de base de données ou d’application spécifique. L’objectif de ce test est de fournir des indications quant aux performances relatives d’une base de données que l’on peut escompter lors d’un changement de niveau de performance. En réalité, les bases de données varient en termes de taille et de complexité. Elles supportent diverses charges de travail et réagissent de différentes façons. Par exemple, une application gourmande en E/S peut atteindre rapidement les seuils d’E/S, de la même manière qu’une application gourmande en ressources processeur peut atteindre rapidement les limites processeur. Rien ne peut garantir qu’une base de données particulière évoluera de la même façon que le test d’évaluation sous une charge plus importante.

Le test d’évaluation et sa méthodologie sont décrits plus en détail ci-dessous.

### <a name="benchmark-summary"></a>Résumé du test d’évaluation
Le test ASDB mesure les performances d’une série d’opérations de base de données basiques que l’on rencontre le plus fréquemment dans les charges de travail de traitement transactionnel en ligne (OLTP). Bien qu’il ait été conçu pour les environnements cloud, le schéma de base de données, le remplissage des données et les transactions ont été formulés pour représenter les éléments de base couramment utilisés dans les charges de travail OLTP.

### <a name="schema"></a>Schéma
Le schéma a été conçu de façon suffisamment variée et complexe pour prendre en charge un large éventail d’opérations. Le test d’évaluation s’exécute sur une base de données composée de six tables. Les tables se répartissent en trois catégories : taille fixe, extensibles et évolutives. Il existe deux tables de taille fixe, trois tables extensibles et une table évolutive. Les tables de taille fixe comportent un nombre constant de lignes. Les tables extensibles ont une cardinalité proportionnelle aux performances de la base de données, mais qui ne varie pas pendant le test d’évaluation. La table évolutive est dimensionnée à la manière d’une table extensible sur la charge initiale, mais la cardinalité change pendant l’exécution du test d’évaluation à mesure que des lignes sont insérées et supprimées.

Le schéma inclut divers types de données, notamment des entiers, des valeurs numériques, des caractères et des valeurs date/heure. Le schéma inclut des clés primaires et secondaires, mais aucune clé étrangère ; autrement dit, il n’y a pas de contraintes d’intégrité référentielle entre les tables.

Un programme de génération de données génère les données pour la base de données initiale. Les entiers et les données numériques sont générés à l’aide de différentes stratégies. Dans certains cas, les valeurs sont distribuées de façon aléatoire sur une plage. Dans d’autres cas, un ensemble de valeurs est permuté de façon aléatoire pour garantir une distribution spécifique. Les champs de texte sont générés à partir d’une liste pondérée de mots permettant de produire des données réalistes.

La base de données est dimensionnée selon un « facteur d’échelle ». Le facteur d’échelle (« SF ») détermine la cardinalité des tables extensibles et évolutives. Comme décrit dans la section « Utilisateurs et rythme » ci-dessous, la taille de la base de données, le nombre d’utilisateurs et les performances maximales s’adaptent tous au prorata des uns des autres.

### <a name="transactions"></a>Transactions
La charge de travail se compose de neuf types de transactions, comme indiqué dans le tableau ci-dessous. Chaque transaction est conçue pour mettre en évidence un ensemble particulier de caractéristiques système dans le moteur de base de données et le matériel système, en les distinguant clairement des autres transactions. Cette approche permet d’évaluer plus facilement l’impact des différents composants sur les performances globales. Par exemple, la transaction « Read Heavy » génère un nombre important d’opérations de lecture à partir du disque.

| Type de transaction | Description |
| --- | --- |
| Read Lite |SÉLECTION ; dans la mémoire ; lecture seule |
| Read Medium |SÉLECTION ; principalement dans la mémoire ; lecture seule |
| Read Heavy |SÉLECTION ; principalement hors de la mémoire ; lecture seule |
| Update Lite |MISE À JOUR ; dans la mémoire ; lecture-écriture |
| Update Heavy |MISE À JOUR ; principalement hors de la mémoire ; lecture-écriture |
| Insert Lite |INSERTION ; dans la mémoire ; lecture-écriture |
| Insert Heavy |INSERTION ; principalement hors de la mémoire ; lecture-écriture |
| Supprimer |SUPPRESSION ; combinaison de ressources dans la mémoire et hors de la mémoire ; lecture-écriture |
| CPU Heavy |SÉLECTION ; dans la mémoire ; charge UC relativement importante ; lecture seule |

### <a name="workload-mix"></a>Combinaison de charges de travail
Les transactions sont sélectionnées de manière aléatoire à partir d’une distribution pondérée avec la combinaison générale suivante. La combinaison générale présente un ratio lecture/écriture d’environ 2:1.

| Type de transaction | % de la combinaison |
| --- | --- |
| Read Lite |35 |
| Read Medium |20 |
| Read Heavy |5. |
| Update Lite |20 |
| Update Heavy |3 |
| Insert Lite |3 |
| Insert Heavy |2 |
| Supprimer |2 |
| CPU Heavy |10 |

### <a name="users-and-pacing"></a>Utilisateurs et rythme
La charge de travail d’évaluation est pilotée par un outil qui envoie des transactions sur un ensemble de connexions afin de simuler le comportement d’un nombre d’utilisateurs simultanés. Bien que l’ensemble des connexions et transactions soient générées de façon automatique, nous appellerons ici ces connexions « utilisateurs » par souci de commodité. Bien que chaque utilisateur fonctionne indépendamment de tous les autres, tous exécutent le même cycle d’étapes, comme indiqué ci-dessous :

1. Établir une connexion à la base de données.
2. Répéter jusqu’à obtention du signal de sortie :
   * Sélection d’une transaction de façon aléatoire (à partir d’une distribution pondérée)
   * Exécution de la transaction sélectionnée et évaluation du temps de réponse
   * Délai d’attente
3. Fermer la connexion à la base de données.
4. Quitter.

Le délai (à l’étape 2c) est sélectionné au hasard, mais avec une distribution de 1 seconde en moyenne. Chaque utilisateur peut donc, en moyenne, générer au maximum une transaction par seconde.

### <a name="scaling-rules"></a>Règles de mise à l’échelle
Le nombre d’utilisateurs est déterminé par la taille de la base de données (en unités de facteur d’échelle). On compte un seul utilisateur pour cinq unités de facteur d’échelle. En raison du délai, un même utilisateur peut générer, en moyenne, au maximum une transaction par seconde.

Par exemple, une base de données utilisant un facteur d’échelle de 500 (SF = 500) comportera 100 utilisateurs et pourra atteindre un taux maximum de 100 TPS. Pour augmenter le taux de TPS, la base de données doit être plus volumineuse et comporter un plus grand nombre d’utilisateurs.

### <a name="measurement-duration"></a>Durée de la mesure
Pour être reconnu valide, un test d’évaluation doit s’effectuer sur une durée de mesure constante d’au moins une heure.

### <a name="metrics"></a>Mesures
Le débit et le temps de réponse constituent les principaux indicateurs du test d’évaluation.

* Le débit est la mesure de performance la plus importante dans ce test d’évaluation. Il est exprimé en transactions par unité de temps et tient compte de tous les types de transactions.
* Le temps de réponse permet de mesurer la prévisibilité des performances. La limite de temps de réponse varie en fonction de la classe de service, les classes de service plus élevées devant satisfaire à des exigences de temps de réponse plus serrées, comme indiqué ci-dessous.

| Classe de service | Mesure du débit | Temps de réponse requis |
| --- | --- | --- |
| Premium |Transactions par seconde |95e centile à 0,5 seconde |
| standard |Transactions par minute |90e centile à 1 seconde |
| De base |Transactions par heure |80e centile à 2 secondes |


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles pour les bases de données uniques, consultez [Limites des ressources DTU SQL Database pour les bases de données uniques](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles pour les pools élastiques, consultez [Limites des ressources DTU SQL Database pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
