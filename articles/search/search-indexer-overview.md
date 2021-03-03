---
title: Présentation de l’indexeur
titleSuffix: Azure Cognitive Search
description: Analysez Azure SQL Database, SQL Managed Instance, Azure Cosmos DB ou Stockage Azure pour extraire les données pouvant être recherchées et remplir un index Recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: a274e96defa8b6b74c046923d87f198029399dd4
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098093"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexeurs dans Recherche cognitive Azure

Dans Recherche cognitive Azure, un *indexeur* est un analyseur qui extrait le texte et les métadonnées pouvant faire l’objet d’une recherche à partir d’une source de données Azure externe et qui renseigne un index de recherche en fonction des mappages champ à champ entre la source de données et votre index. Cette approche est parfois appelée « modèle d’extraction », car le service extrait des données sans que vous ayez à écrire un code qui ajoute des données à un index. Les indexeurs permettent également d’améliorer les fonctionnalités d’[enrichissement par IA](cognitive-search-concept-intro.md) de Recherche cognitive, en intégrant le traitement externe du contenu à destination d’un index.

Les indexeurs sont spécifiques à Azure, avec des indexeurs individuels pour [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Stockage Table Azure](search-howto-indexing-azure-tables.md) et [Stockage Blob](search-howto-indexing-azure-blob-storage.md). Lors de la configuration d’un indexeur, vous spécifiez une source de données (origine), ainsi qu’un index (destination). Plusieurs sources, telles que Stockage Blob, ont des propriétés de configuration supplémentaires spécifiques à ce type de contenu.

Vous pouvez exécuter des indexeurs à la demande ou en fonction d’une planification d’actualisation des données périodique qui s’exécute jusqu’à une fois toutes les cinq minutes. Des mises à jour plus fréquentes requièrent un [modèle d’émission](search-what-is-data-import.md) qui met à jour simultanément les données dans Recherche cognitive Azure et dans votre source de données externe.

## <a name="usage-scenarios"></a>Scénarios d’usage

Vous pouvez utiliser un indexeur comme seul moyen d’ingestion des données ou dans le cadre d’une combinaison de techniques qui chargent et éventuellement transforment ou enrichissent le contenu en même temps. Le tableau suivant récapitule les scénarios principaux.

| Scénario |Stratégie |
|----------|---------|
| Source de données unique | Ce modèle est le plus simple : une source de données est le seul fournisseur de contenu pour un index de recherche. À partir de la source, vous identifiez un champ contenant des valeurs uniques qui sert de clé de document dans l’index de recherche. La valeur unique sera utilisée comme identificateur. Tous les autres champs sources sont mappés implicitement ou explicitement aux champs correspondants dans un index. </br></br>Un point important à retenir est que la valeur d’une clé de document provient des données sources. Un service de recherche ne génère pas de valeurs de clés. Lors des exécutions suivantes, les documents entrants avec de nouvelles clés sont ajoutés, tandis que les documents entrants avec des clés existantes sont fusionnés ou écrasés, selon que les champs d’index ont la valeur null ou sont remplis. |
| Sources de données multiples | Un index peut accepter du contenu provenant de plusieurs sources, chaque exécution apportant un nouveau contenu à partir d’une source différente. </br></br>Un résultat peut être un index qui obtient des documents après chaque exécution de l’indexeur, avec des documents entiers créés dans leur intégralité à partir de chaque source. Par exemple, les documents 1 à 100 proviennent du Stockage Blob, les documents 101 à 200 proviennent d’Azure SQL, etc. Le défi de ce scénario consiste à concevoir un schéma d’index qui fonctionne pour toutes les données entrantes et une structure de clé de document uniforme dans l’index de recherche. En mode natif, les valeurs qui identifient de façon unique un document sont metadata_storage_path dans un conteneur d’objets blob et une clé primaire dans une table SQL. Vous pouvez imaginer qu’une ou les deux sources doivent être modifiées pour fournir des valeurs de clés dans un format commun, indépendamment de l’origine du contenu. Pour ce scénario, attendez-vous à effectuer un certain niveau de prétraitement pour homogénéiser les données afin qu’elles puissent être extraites dans un index unique. </br></br>Un autre résultat peut être la recherche de documents partiellement remplis lors de la première exécution, puis complétés lors des exécutions suivantes avec des valeurs provenant d’autres sources. Par exemple, les champs 1 à 10 proviennent du Stockage blob, les objets 11 à 20 proviennent d’Azure SQL, etc. Le défi de ce modèle est de s’assurer que chaque exécution d’indexation cible le même document. La fusion de champs dans un document existant requiert une correspondance sur la clé de document. Pour obtenir un exemple de ce scénario, consultez le [Tutoriel : Indexer à partir de plusieurs sources de données](tutorial-multiple-data-sources.md). |
| Plusieurs Indexeurs | Si vous utilisez plusieurs sources de données, vous aurez peut-être besoin de plusieurs indexeurs si vous devez faire varier les paramètres d’exécution, la planification ou les mappages de champs. Même si plusieurs jeux indexeur-données-source peuvent cibler le même index, gardez à l’esprit qu’une exécution d’indexeur est susceptible de remplacer les valeurs existantes dans l’index. Si un deuxième jeu indexeur-données-source cible les mêmes documents et champs, toutes les valeurs de la première exécution sont remplacées. Les valeurs de champ sont remplacées dans leur entier ; un indexeur ne peut pas fusionner les valeurs de plusieurs exécutions dans le même champ.</br></br>Un autre cas d’utilisation de plusieurs indexeurs est le [scale-out interrégion de Recherche cognitive](search-performance-optimization.md#use-indexers-for-updating-content-on-multiple-services). Vous pouvez avoir des copies du même index de recherche dans différentes régions. Pour synchroniser le contenu de l’index de recherche, vous pouvez faire en sorte que plusieurs indexeurs effectuent une extraction de la même source de données, avec chaque indexeur ciblant un index de recherche différent.</br></br>L’[indexation parallèle](search-howto-large-index.md#parallel-indexing) de jeux de données très volumineux nécessite également une stratégie multi-indexeur. Chaque indexeur cible un sous-ensemble des données. |
| Transformation du contenu | Recherche cognitive prend en charge les comportements [d’enrichissement par IA](cognitive-search-concept-intro.md) facultatifs qui ajoutent l’analyse d’images et le traitement en langage naturel pour créer des contenus et structures nouveaux pouvant faire l’objet d’une recherche. L’enrichissement par IA est piloté par l’indexeur, par le biais d’un [ensemble de compétences](cognitive-search-working-with-skillsets.md) joint. Pour effectuer l’enrichissement par IA, l’indexeur a toujours besoin d’un index et d’une source de données Azure, mais dans ce scénario, il ajoute le traitement par l’ensemble de compétences à l’exécution de l’indexeur. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Sources de données prises en charge

Les indexeurs analysent les magasins de données sur Azure.

+ [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (en préversion)
+ [Stockage de tables Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server sur les machines virtuelles Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

Les connexions de l’indexeur aux sources de données distantes peuvent être effectuées à l’aide de connexions Internet standard (publiques) ou de connexions privées chiffrées quand vous utilisez des réseaux virtuels Azure pour les applications clientes. Vous pouvez également configurer des connexions pour l’authentification à l’aide d’une identité de service approuvée. Pour plus d’informations sur les connexions sécurisées, consultez [Octroi de l’accès via des points de terminaison privés](search-indexer-securing-resources.md#granting-access-via-private-endpoints) et [Se connecter à une source de données en utilisant une identité managée](search-howto-managed-identities-data-sources.md).

## <a name="stages-of-indexing"></a>Étapes de l’indexation

Lors d’une exécution initiale, lorsque l’index est vide, un indexeur lit toutes les données fournies dans la table ou le conteneur. Lors des exécutions suivantes, l’indexeur peut généralement détecter et récupérer uniquement les données qui ont été modifiées. Pour les données blob, la détection des modifications est automatique. Pour d’autres sources de données comme Azure SQL ou Cosmos DB, la détection des modifications doit être activée.

Pour chaque document qu’il reçoit, un indexeur implémente ou coordonne plusieurs étapes, de la récupération du document à un « transfert » vers un moteur de recherche final pour indexation. Si vous le souhaitez, un indexeur peut également opérer de manière instrumentale pour gérer l’exécution d’un ensemble de compétences et des sorties, en supposant qu’un ensemble de compétences est défini.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Étapes de l'indexeur" border="false":::

### <a name="stage-1-document-cracking"></a>Étape 1 : Décodage du document

Le décodage de document est le processus d’ouverture de fichiers et d’extraction du contenu. Selon le type de source de données, l’indexeur essaiera d’effectuer différentes opérations pour extraire le contenu potentiellement indexable.  

Exemples :  

+ Si le document est un enregistrement d’une [source de données Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), l’indexeur extrait chacun des champs de l’enregistrement.
+ Si le document est un fichier PDF d’une [source de données Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md), l’indexeur extrait le texte, les images et les métadonnées.
+ Si le document est un enregistrement d’une [source de données Cosmos DB](search-howto-index-cosmosdb.md), l’indexeur extrait les champs et les sous-champs du document Cosmos DB.

### <a name="stage-2-field-mappings"></a>Étape 2 : Mappages de champs 

Un indexeur extrait le texte d’un champ source et l’envoie à un champ de destination dans un index ou une base de connaissances. Si les noms et les types de champs coïncident, le chemin d’accès est validé. Toutefois, vous pouvez utiliser des noms ou des types différents dans la sortie, auquel cas vous devez indiquer à l’indexeur comment mapper le champ. 

Cette étape survient après le décodage du document, mais avant les transformations, lorsque l’indexeur lit les données des documents sources. Lorsque vous définissez un [mappage de champs](search-indexer-field-mappings.md), la valeur du champ source est envoyée telle quelle au champ de destination, sans aucune modification. 

### <a name="stage-3-skillset-execution"></a>Étape 3 : Exécution d’un ensemble de compétences

L’exécution d’un ensemble de compétences est une étape facultative qui appelle un traitement IA intégré ou personnalisé. Vous pouvez en avoir besoin pour la reconnaissance optique de caractères (OCR) sous la forme d’une analyse d’images si les données sources sont une image binaire, ou dans le cas d’une traduction linguistique si le contenu est dans d’autres langues. 

Quelle que soit la transformation, l’exécution d’un ensemble de compétences est l’endroit où l’enrichissement se produit. Si un indexeur est un pipeline, vous pouvez considérer un [ensemble de compétences](cognitive-search-defining-skillset.md) comme un « pipeline dans le pipeline ».

### <a name="stage-4-output-field-mappings"></a>Étape 4 : Mappages de champs de sortie

Si vous incluez un ensemble de compétences, vous devrez probablement inclure des mappages de champs de sortie. La sortie d’un ensemble de compétences est en fait une arborescence d’informations appelée *document enrichi*. Les mappages de champs de sortie vous permettent de sélectionner les parties de cette arborescence à mapper dans les champs de votre index. Découvrez comment [définir des mappages de champs de sortie](cognitive-search-output-field-mapping.md).

Alors que les mappages de champs associent les valeurs verbatim de la source de données aux champs de destination, les mappages de champs de sortie indiquent à l’indexeur comment associer les valeurs transformées dans le document enrichi aux champs de destination dans l’index. Contrairement aux mappages de champs, qui sont considérés comme facultatifs, vous devrez toujours définir un mappage de champs de sortie pour tout contenu transformé qui figurera dans un index.

L’image suivante montre un exemple de [session de débogage](cognitive-search-debug-session.md) représentant les étapes de l’indexeur : le décodage de document, les mappages de champs, l’exécution d’un ensemble de compétences, et les mappages de champs de sortie.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="exemple de session de débogage" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Flux de travail de base

Les indexeurs peuvent offrir des fonctionnalités propres à la source de données. À cet égard, certains aspects de la configuration de l’indexeur ou de la source de données varient en fonction du type d’indexeur. Cependant, tous les indexeurs présentent une composition et des exigences de base identiques. Les étapes communes à tous les indexeurs sont décrites ci-dessous.

### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d'une source de données

Les indexeurs nécessitent un objet *source de données* qui fournit une chaîne de connexion et éventuellement des informations d’identification. Appelez l’API de [création de source de données (REST)](/rest/api/searchservice/create-data-source) ou la [classe SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) pour créer la ressource.

Les sources de données sont configurées et gérées indépendamment des indexeurs qui les utilisent. Autrement dit, une source de données peut être utilisée par plusieurs indexeurs pour charger plusieurs index à la fois.

### <a name="step-2-create-an-index"></a>Étape 2 : Création d'un index

Un indexeur automatise certaines tâches liées à l’ingestion des données, mais la création d’un index n’en fait généralement pas partie. Au préalable, vous devez disposer d’un index prédéfini présentant des champs qui correspondent à ceux de votre source de données externe. Les champs doivent correspondre par nom et type de données. Si ce n’est pas le cas, vous pouvez [définir des mappages de champs](search-indexer-field-mappings.md) pour établir l’association. Pour plus d’informations sur la structuration d’un index, consultez [Créer un index (REST)](/rest/api/searchservice/Create-Index) ou [Classe SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Bien que les indexeurs ne puissent pas générer d’index pour vous, l’Assistant **Importation des données** du portail peut vous aider. Dans la plupart des cas, l’Assistant peut déduire un schéma d’index à partir des métadonnées existantes dans la source, en présentant un schéma d’index préliminaire que vous pouvez modifier en ligne pendant que l’Assistant est actif. Une fois que l’index est créé sur le service, les modifications supplémentaires dans le portail sont principalement limitées à l’ajout de nouveaux champs. Pensez à utiliser l’Assistant pour créer un index (mais pas pour le réviser). Pour mettre vos connaissances en pratique, parcourez la [procédure pas à pas dans le portail](search-get-started-portal.md).

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>Étape 3 : Créer et exécuter (ou planifier) l’indexeur

Un indexeur s’exécute quand vous [créez un indexeur](/rest/api/searchservice/Create-Indexer) pour la première fois sur le service de recherche. Ce n’est qu’au moment où vous créez ou exécutez l’indexeur que vous découvrez si la source de données est accessible ou si les compétences sont valides. Après la première exécution, vous pouvez le réexécuter à la demande à l’aide de l’article [Exécuter un indexeur](/rest/api/searchservice/run-indexer), ou vous pouvez [définir une planification périodique](search-howto-schedule-indexers.md). 

Vous pouvez surveiller l’état de l’indexeur dans le portail ou à l’aide de [l’API Get Indexer Status](/rest/api/searchservice/get-indexer-status). Vous devez également [exécuter des requêtes sur l’index](search-query-create.md) pour vérifier que le résultat correspond à ce que vous attendiez.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez à présent passer en revue les propriétés et paramètres des indexeurs, la planification et la supervision des indexeurs. Vous pouvez également revenir à la liste des [sources de données prises en charge](#supported-data-sources) pour plus d’informations sur une source spécifique.

+ [Créer des indexeurs](search-howto-create-indexers.md)
+ [Réinitialiser et exécuter des indexeurs](search-howto-run-reset-indexers.md)
+ [Planifier des indexeurs](search-howto-schedule-indexers.md)
+ [Définir des mappages de champs](search-indexer-field-mappings.md)
+ [Superviser l’état d’un indexeur](search-howto-monitor-indexers.md)