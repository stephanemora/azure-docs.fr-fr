---
title: Modèles d’achat Azure SQL Database | Microsoft Docs
description: Découvrez le modèle d’achat pour Azure SQL Database.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: f40fe3da6874d8656c9c0a0ddce9fed602cb25f9
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091913"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Ressources et modèles d’achat Azure SQL Database 

Les serveurs logiques dans [Azure SQL Database](sql-database-technical-overview.md) proposent deux modèles d’achat pour les ressources de calcul, de stockage et d’E/S : un modèle d’achat DTU et un modèle d’achat vCore. 

> [!NOTE]
> Les [instances managées](sql-database-managed-instance.md) dans Azure SQL Database offrent uniquement le modèle d’achat vCore.

Le tableau et le graphique suivants comparent ces deux modèles d’achat.

> [!IMPORTANT]
> Pour le modèle d’achat vCore, voir [Modèle d’achat vCore](sql-database-service-tiers-vcore.md).

|**Modèle d’achat**|**Description**|**Idéal pour**|
|---|---|---|
|Modèle DTU|Ce modèle est basé sur une mesure regroupant des ressources de calcul, de stockage et d’E/S. Les niveaux de performance en termes d’unités de transaction de base de données (DTU) pour des bases de données uniques et d’unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour en savoir plus sur les DTU et les eDTU, consultez [Définition des DTU et des eDTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).|Idéal pour les clients qui souhaitent des options de ressources préconfigurées et simples.| 
|Modèle vCore|Ce modèle vous permet de faire évoluer indépendamment les ressources de calcul et de stockage. Il permet également d’utiliser Azure Hybrid Benefit pour SQL Server afin de réduire les coûts.|Idéal pour les clients qui donnent la priorité à la flexibilité, au contrôle et à la transparence.|
||||  

![Modèle de prix](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore 

Un vCore est l’UC logique qui permet de choisir parmi plusieurs générations de matériel. Le modèle d’achat vCore apporte flexibilité, contrôle et transparence pour la consommation des ressources. En outre, il permet de traduire facilement les exigences des charges de travail locales pour le cloud. Ce modèle permet de mettre à l’échelle le calcul, la mémoire et le stockage en fonction des besoins des charges de travail. Dans le modèle d’achat vCore, les clients peuvent choisir entre les niveaux de service Usage général et Critique pour l’entreprise pour les [bases de données uniques](sql-database-single-database-scale.md) et pour les [pools élastiques](sql-database-elastic-pool.md). 

Le modèle d’achat vCore vous permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts. Si votre base de données ou pool élastique consomme plus de 300 DTU, le passage au modèle vCore peut vous permettre de réaliser des économies. Vous pouvez passer au modèle vCore à l’aide de l’API de votre choix ou du portail Azure, sans aucun temps d’arrêt. Le passage au modèle vCore n’est toutefois pas obligatoire. Si le modèle d’achat DTU répond à vos besoins métier et à vos besoins en performance, vous devez continuer de l’utiliser. Si vous décidez de passer du modèle DTU au modèle vCore, vous devez sélectionner le niveau de performance en suivant la règle de base suivante : chaque groupe de 100 DTU du niveau Standard nécessite au moins 1 vCore dans le niveau Usage général, et chaque groupe de 125 DTU du niveau Premium nécessite au moins 1 vCore Critique pour l'entreprise.

Dans le modèle d’achat vCore, les clients paient pour :
- Le calcul (niveau de service + nombre de vCore + génération du matériel)*
- Le type et la quantité de stockage des journaux et des données 
- Le nombre d’E/S**
- Le stockage de sauvegarde (RA-GRS)** 

\* Dans la préversion publique initiale, les processeurs logiques Gen 4 sont basés sur des processeurs Intel E5-2673 v3 (Haswell) 2.4 GHz.

\*\* Dans la préversion, les sauvegardes et les E/S sont gratuites pendant 7 jours.

> [!IMPORTANT]
> Le calcul, les E/S, ainsi que le stockage des données et des journaux, sont facturés au niveau de chaque base de données ou au niveau du pool élastique. Le stockage des sauvegardes est facturé au niveau de chaque base de données. Pour plus d’informations sur les frais liés à Managed Instance, consultez [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> **Limitations liées aux régions :** le modèle d’achat vCore n’est pas encore disponible dans les régions suivantes : Europe de l’Ouest, France Centre, Royaume-Uni Sud, Royaume-Uni Ouest et Sud-Est de l’Australie.

## <a name="dtu-based-purchasing-model"></a>Modèle d’achat DTU

L’unité DTU (Database Throughput Unit) correspond à une mesure mixte d’UC, de mémoire, de lectures et d’écritures. Le modèle d’achat DTU offre un ensemble préconfiguré de ressources de calcul et de stockage inclus pour obtenir différents niveaux de performance d’application. Les clients qui préfèrent la simplicité d’un ensemble préconfiguré et de versements mensuels peuvent trouver le modèle DTU plus adapté à leurs besoins. Dans le modèle d’achat DTU, les clients peuvent choisir parmi les niveaux de service **De base**, **Standard** et **Premium** pour [chaque base de données](sql-database-single-database-scale.md) et pour les [pools élastiques](sql-database-elastic-pool.md). 

### <a name="what-are-database-transaction-units-dtus"></a>Définition des unités de transaction de base de données (DTU)
Microsoft garantit un certain niveau de ressources pour une seule base de données Azure SQL Database à un niveau de performances spécifique au sein d’un [niveau de service](sql-database-single-database-scale.md) (indépendamment de toute autre base de données dans le cloud Azure), fournissant un niveau de performances prévisible. Le volume de ressources est calculé sous forme d’unités de transactions de base de données ou DTU. Il s’agit d’une mesure groupée de calcul, de stockage et de ressources d’E/S. Le ratio entre ces ressources a été déterminé à l’origine par une [charge de travail d’évaluation OLTP](sql-database-benchmark-overview.md), conforme aux charges de travail OLTP réelles standard. Quand votre charge de travail dépasse la quantité de ces ressources, le débit est limité, ce qui entraîne un ralentissement des performances et des délais d’attente. Les ressources utilisées par votre charge de travail n’impactent pas les ressources disponibles pour les autres bases de données SQL dans le cloud Azure, et les ressources utilisées par d’autres charges de travail n’impactent pas les ressources disponibles pour votre base de données SQL.

![cadre englobant](./media/sql-database-what-is-a-dtu/bounding-box.png)

Les DTU sont particulièrement utiles pour comprendre la quantité relative de ressources entre les bases de données Azure SQL Database à différents niveaux de performances et de service. Par exemple, le fait de doubler les DTU en augmentant le niveau de performances d’une base de données revient à doubler l’ensemble des ressources disponibles pour cette base de données. Par exemple, une base de données Premium P11 comprenant 1 750 DTU fournit une puissance de calcul DTU 350 fois plus importante qu’une base de données de base comprenant 5 DTU.  

Pour avoir une idée plus précise de la consommation de ressources (DTU) de votre charge de travail, utilisez [Query Performance Insight pour Azure SQL Database ](sql-database-query-performance.md) pour :

- Identifier les principales requêtes par UC/durée/nombre d’exécutions qui peuvent être réglées pour améliorer les performances. Par exemple, une requête utilisant beaucoup d’E/S peut tirer profit de l’utilisation de [techniques d’optimisation en mémoire](sql-database-in-memory.md) pour mieux utiliser la mémoire disponible à un certain niveau de service et de performances.
- Connaître les détails d’une requête, afficher son texte et l’historique d’utilisation des ressources.
- Accéder aux recommandations de réglage des performances qui indiquent les actions effectuées par [SQL Database Advisor](sql-database-advisor.md).

Vous pouvez changer les [niveaux de service des DTU](sql-database-service-tiers-dtu.md) à tout moment avec un temps d’arrêt minimal de votre application (généralement sous les quatre secondes environ). Pour de nombreuses entreprises et applications, la possibilité de créer des bases de données et d’augmenter ou ralentir les performances à la demande se révèle suffisante, surtout si les modèles d’utilisation sont relativement prévisibles. Mais si vous avez des modèles d'utilisation imprévisibles, il peut être difficile de gérer les coûts et votre modèle commercial. Pour ce scénario, vous utilisez un pool élastique avec un certain nombre d’eDTU qui sont partagées entre plusieurs bases de données dans le pool.

![Introduction à la base de données SQL : DTU de base de données unique par couche et niveau](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Définition des unités de transaction de base de données élastique (DTU)
Plutôt que de fournir un ensemble de ressources (DTU) dédié qui peut ne pas s’avérer toujours nécessaire pour une base de données SQL Database qui est toujours disponible, vous pouvez placer les bases de données dans un [pool élastique](sql-database-elastic-pool.md) sur un serveur SQL Database qui partage un pool de ressources entre ces bases de données. Les ressources partagées dans un pool élastique sont mesurées en eDTU (elastic Database Transaction Unit). Les pools élastiques offrent une solution simple et économique pour gérer les objectifs de performance de plusieurs bases de données ayant des modèles d’utilisation variables et non prévisibles. Un pool élastique garantit que les ressources ne peuvent pas être consommées par une base de données dans le pool, mais également que chaque base de données dans le pool dispose toujours d’une quantité de ressources minimale. 

![Introduction à la base de données SQL : eDTU par couche et niveau](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Un pool bénéficie d’un nombre défini d’eDTU pour un prix donné. Au sein du pool élastique, les différentes bases de données peuvent s’adapter facilement et automatiquement aux limites définies. Une base de données soumise à une charge plus élevée consomme plus d’eDTU pour répondre à la demande. Les bases de données soumises à des charges plus légères consomment moins d’eDTU. Les bases de données qui ne subissent aucune charge ne consomment aucune eDTU. Le provisionnement des ressources pour l’ensemble du pool, plutôt que pour chaque base de données, simplifie les tâches de gestion, fournissant un aperçu du budget nécessaire pour le pool.

Vous pouvez ajouter des eDTU à un pool existant sans que les bases de données du pool ne connaissent de temps d’arrêt et ne soient affectées. De même, si les eDTU supplémentaires ne sont plus nécessaires, elles peuvent être supprimées à partir d’un pool existant à tout moment. Vous pouvez ajouter des bases de données dans le pool ou en supprimer. Vous pouvez également limiter la quantité d’eDTU qu’une base de données peut utiliser en cas de charge importante pour réserver des eDTU pour les autres bases de données. Si vous prévoyez qu’une base de données sous-utilise des ressources, vous pouvez la déplacer hors du pool et la configurer en tant que base de données unique avec une quantité prévisible des ressources nécessaires.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Comment puis-je déterminer le nombre de DTU requises par ma charge de travail ?
Si vous cherchez à migrer une charge de travail de machine virtuelle SQL Server ou locale existante vers une base de données SQL Azure, vous pouvez utiliser l’outil [DTU Calculator](http://dtucalculator.azurewebsites.net/) pour évaluer approximativement le nombre de DTU requises. Dans le cas d’une charge de travail de base de données SQL Azure existante, vous pouvez utiliser [Query Performance Insight pour base de données SQL](sql-database-query-performance.md) pour comprendre votre consommation des ressources de la base de données (DTU) et obtenir un insight plus approfondi pour l’optimisation de votre charge de travail. Vous pouvez également utiliser la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) pour voir la consommation des ressources au cours de la dernière heure. Sinon, la vue de catalogue [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) affiche la consommation des ressources pour les 14 derniers jours, mais avec une précision inférieure (moyennes de cinq minutes).

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Comment savoir si je peux tirer parti d’un pool élastique de ressources ?
Les pools sont idéaux dans le cas de nombreuses bases de données avec des modèles d’utilisation spécifiques. Pour une base de données indiquée, ce modèle se caractérise par une moyenne d’utilisation faible avec des pics d’utilisation relativement rares. La base de données SQL évalue automatiquement l’historique d’utilisation en ressources des bases de données dans un serveur de base de données SQL existant et recommande la configuration de pool appropriée dans le portail Azure. Pour plus d’informations, consultez l’article [Quand utiliser un pool élastique ?](sql-database-elastic-pool.md).

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Que se passe-t-il lorsque j’atteins le nombre maximal de DTU ?
Les niveaux de performances sont étalonnés et régis pour fournir les ressources nécessaires à l’exécution de la charge de travail de votre base de données dans les limites maximales autorisées pour le niveau de service/niveau de performances sélectionné. Si votre charge de travail atteint une des limites UC, E/S de données ou E/S de journal, vous bénéficiez toujours du niveau maximal de ressources autorisé, mais les latences de requête risquent de s’accroître. Ces limites ne génèrent pas d’erreur, plutôt un ralentissement de la charge de travail, sauf si le ralentissement s’accentue au point que les requêtes arrivent à expiration. Si vous atteignez le nombre maximal autorisé de sessions/demandes utilisateur simultanées (threads de travail), vous voyez des erreurs explicites. Pour plus d’informations sur les limites de ressource qui ne concernent pas l’unité centrale, la mémoire, les E/S de données ou les E/S du journal des transactions, consultez [Limites de ressources d’Azure SQL Database]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached).

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

Le tableau ci-dessous indique le nombre d’utilisateurs effectivement pris en charge pour chaque niveau de service et de performance.

| Niveau de service (niveau de performance) | Utilisateurs | Taille de la base de données |
| --- | --- | --- |
| De base |5. |720 Mo |
| Standard (S0) |10 |1 Go |
| Standard (S1) |20 |2,1 Go |
| Standard (S2) |50 |7,1 Go |
| Premium (P1) |100 |14 Go |
| Premium (P2) |200 |28 Go |
| Premium (P6) |800 |114 Go |

### <a name="measurement-duration"></a>Durée de la mesure
Pour être reconnu valide, un test d’évaluation doit s’effectuer sur une durée de mesure constante d’au moins une heure.

### <a name="metrics"></a>Mesures
Le débit et le temps de réponse constituent les principaux indicateurs du test d’évaluation.

* Le débit est la mesure de performance la plus importante dans ce test d’évaluation. Il est exprimé en transactions par unité de temps et tient compte de tous les types de transactions.
* Le temps de réponse permet de mesurer la prévisibilité des performances. La limite de temps de réponse varie en fonction de la classe de service, les classes de service plus élevées devant satisfaire à des exigences de temps de réponse plus serrées, comme indiqué ci-dessous.

| Classe de service | Mesure du débit | Temps de réponse requis |
| --- | --- | --- |
| Premium |Transactions par seconde |95e centile à 0,5 seconde |
| Standard |Transactions par minute |90e centile à 1 seconde |
| De base |Transactions par heure |80e centile à 2 secondes |

## <a name="next-steps"></a>Étapes suivantes

- Pour le modèle d’achat vCore, voir [Modèle d’achat vCore](sql-database-service-tiers-vcore.md).
- Pour le modèle d’achat basé sur les DTU, consultez [Modèle d’achat DTU](sql-database-service-tiers-dtu.md).
