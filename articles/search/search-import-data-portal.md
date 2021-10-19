---
title: Importer des données dans un index de recherche à l’aide du Portail Azure
titleSuffix: Azure Cognitive Search
description: En savoir plus sur l’Assistant Importation des données dans le portail Azure, qui permet de créer et de charger un index, et d’appeler éventuellement l’enrichissement par IA à l’aide de compétences intégrées pour le traitement en langage naturel, la traduction, la reconnaissance optique et l’analyse d’images.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 9fe443422bac06ccb934a34799bc29be29c45cb5
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714667"
---
# <a name="import-data-wizard-in-azure-cognitive-search"></a>Assistant Importation des données pour la Recherche cognitive Azure

L'**Assistant Importation des données** dans le portail Azure crée plusieurs objets utilisés pour l’indexation et l’enrichissement par IA sur un service de recherche. Si vous débutez avec Recherche cognitive Azure, il s’agit de l’une des fonctionnalités les plus puissantes à votre disposition. Avec un minimum d’effort, vous pouvez créer un pipeline d’indexation ou d’enrichissement qui exerce la plupart des fonctionnalités de Recherche cognitive Azure.

Si vous utilisez l’Assistant pour le test de preuve de concept, cet article explique le fonctionnement interne de l’Assistant afin que vous puissiez l’utiliser de manière plus efficace.

Cet article n’est pas un guide pas à pas. Pour obtenir de l’aide sur l’utilisation de l’Assistant avec des exemples de données intégrés, consultez le guide [Démarrage rapide : Créer un index de recherche](search-get-started-portal.md) ou [Démarrage rapide : Créer une traduction de texte et un ensemble de compétences d’entité](cognitive-search-quickstart-blob.md).

## <a name="starting-the-wizard"></a>Démarrage de l'Assistant

Dans le [portail Azure](https://portal.azure.com), ouvrez la page du service de recherche à partir du tableau de bord, ou [recherchez votre service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans la liste. En haut de la page de présentation du service, cliquez sur **Importer des données**.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Capture d’écran de la commande Importer des données" border="true":::

L’Assistant s’ouvre entièrement dans la fenêtre du navigateur, ce qui vous permet d’avoir plus de place pour travailler. Plusieurs pages sont assez denses.

Vous pouvez aussi lancer **l’Importation des données** à partir d’autres services Azure, dont Azure Cosmos DB, Azure SQL Database, SQL Managed Instance et le Stockage Blob Azure. Recherchez **Ajouter Recherche cognitive Azure** dans le volet de navigation de gauche de la page de présentation du service.

## <a name="objects-created-by-the-wizard"></a>Objets créés par l'Assistant

L’Assistant génère les objets dans le tableau suivant. Une fois les objets créés, vous pouvez lire leurs définitions JSON dans le portail ou les appeler à partir du code.

| Object | Description | 
|--------|-------------|
| [Indexeur](/rest/api/searchservice/create-indexer)  | Objet de configuration spécifiant une source de données, un index cible, un ensemble de compétences facultatif, une planification facultative et des paramètres de configuration facultatifs pour la gestion des erreurs et l’encodage en base 64. |
| [Source de données](/rest/api/searchservice/create-data-source)  | Conserve les informations de connexion aux données sources, notamment les informations d’identification. Un objet de source de données est utilisé exclusivement avec les indexeurs. | 
| [Index](/rest/api/searchservice/create-index) | Structure de données physique utilisée pour la recherche en texte intégral et d’autres requêtes. | 
| [Ensemble de compétences](/rest/api/searchservice/create-skillset) | facultatif. Ensemble complet d’instructions destiné à manipuler, transformer et mettre en forme du contenu, notamment en analysant et extrayant des informations de fichiers image. À l’exception des structures très simples et limitées, il comporte une référence à une ressource Cognitive Services qui assure l’enrichissement. | 
| [Base de connaissances](knowledge-store-concept-intro.md) | facultatif. Enregistre la sortie d’un [pipeline d’enrichissement par AI](cognitive-search-concept-intro.md) dans des tables et des objets Blob dans Stockage Azure pour une analyse indépendante ou un traitement en aval. |

## <a name="benefits-and-limitations"></a>Avantages et limitations

Avant d’écrire du code, vous pouvez utiliser l’Assistant à des fins de prototypage et de test de preuve de concept. L’Assistant se connecte à des sources de données externes, échantillonne les données pour créer un index initial, puis importe les données sous forme de documents JSON dans un index dans la Recherche cognitive Azure. 

Si vous évaluez des ensembles de compétences, l’Assistant gère tous les mappages de champs de sortie et ajoute des fonctions d’assistance pour créer des objets utilisables. Le fractionnement de texte est ajouté si vous spécifiez un mode d’analyse. La fusion de texte est ajoutée si vous avez choisi l’analyse des images afin que l’Assistant puisse réunir les descriptions de texte et le contenu de l’image. Les compétences Modélisateur ont été ajoutées pour prendre en charge les projections valides si vous avez choisi l’option Base de connaissances. Toutes les tâches ci-dessus sont accompagnées d’une courbe d’apprentissage. Si vous débutez avec l’enrichissement, la possibilité de gérer ces étapes pour vous permet de mesurer la valeur d’une compétence sans devoir investir trop de temps et d’efforts.

L’échantillonnage est le processus par lequel un schéma d’index est déduit et présente quelques limitations. Lorsque la source de données est créée, l’Assistant choisit au hasard un échantillon de documents pour identifier les colonnes qui font partie de la source de données. Tous les fichiers ne sont pas lus, car l’opération pourrait durer des heures avec les sources de données très volumineuses. À partir d’une sélection de documents, les métadonnées sources, comme le nom ou le type de champ, sont utilisées pour créer une collection de champs dans un schéma d’index. Selon la complexité des données sources, vous devrez peut-être modifier le schéma initial dans un souci de précision, ou l’étendre à des fins d’exhaustivité. Vous pouvez faire en sorte que vos modifications soient incorporées dans la page de définition de l’index.

Globalement, les avantages de l’Assistant sont évidents : dans la mesure où les exigences sont respectées, vous pouvez créer un prototype d’index interrogeable en quelques minutes. Certaines complexités liées à l’indexation, comme la sérialisation des données sous forme de documents JSON, sont gérées par l’Assistant.

L’Assistant s’accompagne de limitations. Les contraintes sont les suivantes :

+ L’Assistant ne prend pas en charge l’itération ou la réutilisation. Chaque exécution de l’Assistant donne lieu à la création d’une configuration d’index, d’ensemble de compétences et d’indexeur. Seules les sources de données peuvent être conservées et réutilisées dans l’Assistant. Pour modifier ou affiner d’autres objets, supprimez les objets et recommencez, ou utilisez les API REST ou le kit de développement logiciel (SDK) .NET pour modifier les structures.

+ Le contenu source doit résider dans une [source de données prise en charge](search-indexer-overview.md#supported-data-sources).

+ L’échantillonnage porte sur un sous-ensemble des données sources. Pour les sources de données volumineuses, l’Assistant peut ne pas déceler des champs. Vous serez peut-être amené à étendre le schéma ou à corriger les types de données déduits si l’échantillonnage est insuffisant.

+ L’enrichissement par IA, tel que présenté sur le portail, se limite à un sous-ensemble de compétences intégrées. 

+ Une [base de connaissances](knowledge-store-concept-intro.md), qui peut être créée par l’Assistant, est limitée à quelques projections par défaut et utilise une convention d’affectation de noms par défaut. Si vous souhaitez personnaliser des noms ou des projections, vous devrez créer la base de connaissances par le biais de REST ou des kits de développement logiciel (SDK).

## <a name="workflow"></a>Workflow

L’Assistant est organisé en quatre étapes principales :

1. Il se connecte à une source de données Azure prise en charge.

1. Il crée un schéma d’index, déduit par l’échantillonnage des données sources.

1. Il ajoute éventuellement des enrichissements par ’IA pour extraire ou générer du contenu et la structure. Les entrées pour la création d’une base de connaissances sont collectées à cette étape.

1. Il exécute l’Assistant pour créer des objets, charger des données, définir un calendrier et d’autres options de configuration.

<a name="data-source-inputs"></a>

### <a name="data-source-configuration-in-the-wizard"></a>Configuration de la source de données dans l’Assistant

L’Assistant **Importation des données** se connecte à une [source de données externe prise en charge](search-indexer-overview.md#supported-data-sources) en utilisant la logique interne fournie par les indexeurs Recherche cognitive Azure, qui sont capables d’échantillonner la source, lire les métadonnées, décrypter les documents pour en lire le contenu et la structure, et sérialiser le contenu sous forme de JSON pour une importation ultérieure dans la Recherche cognitive Azure.

Il n’est pas garanti que toutes les sources de données en préversion soient disponibles dans l’Assistant. Étant donné que chaque source de données est susceptible d’introduire d’autres modifications en aval, une source de données en préversion sera uniquement ajoutée à la liste des sources de données si elle prend entièrement en charge toutes les expériences de l’Assistant, telles que la définition des compétences et l’inférence du schéma d’index.

Vous ne pouvez importer des données qu’à partir d’une seule table, d’une vue de base de données ou d’une structure de données équivalente, mais la structure peut inclure des sous-structures hiérarchiques ou imbriquées. Pour plus d’informations, consultez [How to model complex types](search-howto-complex-data-types.md) (Modélisation des types complexes).

### <a name="skillset-configuration-in-the-wizard"></a>Configuration de l’ensemble de compétences dans l’Assistant

La configuration de l’ensemble de compétences se produit après la définition de la source de données, car le type de source de données informe sur la disponibilité de certaines compétences intégrées. En particulier, si vous indexez des fichiers à partir de Stockage Blob, le mode d’analyse choisi pour ces fichiers détermine si l’analyse des sentiments est disponible.

L’Assistant ajoute les compétences que vous choisissez, mais il ajoute également d’autres compétences nécessaires pour obtenir des résultats convaincants. Par exemple, si vous spécifiez une base de connaissances, l’Assistant ajoute une compétence Modélisateur pour prendre en charge les projections (ou les structures de données physiques).

Les ensembles de compétences sont facultatifs et un bouton se trouve au bas de la page pour ignorer si vous ne souhaitez pas utiliser l’enrichissement par IA.

<a name="index-definition"></a>

### <a name="index-schema-configuration-in-the-wizard"></a>Configuration du schéma d’index dans l’Assistant

L’Assistant échantillonne votre source de données pour détecter les champs et le type de champ. En fonction de la source de données, il peut également proposer des champs pour l’indexation des métadonnées.

Étant donné que l’échantillonnage est un exercice imprécis, passez l’index en revue pour tenir compte des points suivants :

1. La liste des champs est-elle exacte ? Si votre source de données contient des champs qui n’ont pas été sélectionnés dans l’échantillonnage, vous pouvez ajouter manuellement tous les nouveaux champs que l’échantillonnage a manqués, et supprimer ceux qui n’ajoutent pas de valeur à une expérience de recherche ou qui ne seront pas utilisés dans une [expression de filtre](search-query-odata-filter.md) ou un [profil de scoring](index-add-scoring-profiles.md).

1. Le type de données convient-il pour les données entrantes ? La Recherche cognitive Azure prend en charge les [types de données EDM (Entity Data Model)](/rest/api/searchservice/supported-data-types). Pour les données Azure SQL, il existe un [tableau de mappages](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) qui présente les valeurs équivalentes. Pour plus d’informations, consultez [Mappages et transformations de champs](search-indexer-field-mappings.md).

1. Avez-vous un champ qui peut faire office de *clé* ? Ce champ doit être Edm.string et doit identifier un document de manière unique. Dans le cas des données relationnelles, elles peuvent être mappées à une clé primaire. Pour les objets blob, il peut s’agir de `metadata-storage-path`. Si des valeurs de champ comportent des espaces ou des tirets, vous devez définir l’option **Clé d’encodage en base-64** dans l’étape **Créer un indexeur**, sous **Options avancées**, pour supprimer le vérification de la validation pour ces caractères.

1. Définissez des attributs pour déterminer comment ce champ est utilisé dans un index. 

   Prenez votre temps dans cette étape, car les attributs déterminent l’expression physique des champs dans l’index. Si, par la suite, vous souhaitez modifier des attributs, même par programmation, vous devrez presque toujours supprimer et regénérer l’index. Les attributs de base comme **Searchable** et **Retrievable** ont un [impact négligeable sur le stockage](search-what-is-an-index.md#index-size). L’activation de filtres et l’utilisation de suggesteurs augmentent les besoins de stockage. 

   + **Possibilité de recherche** permet une recherche en texte intégral. Chaque champ utilisé dans les requêtes de forme libre ou dans les expressions de requête doit avoir cet attribut. Les index inversés sont créés pour chaque champ que vous marquez comme **Possibilité de recherche**.

   + **Récupérable** retourne le champ dans les résultats de la recherche. Chaque champ qui fournit du contenu aux résultats de recherche doit avoir cet attribut. La définition de ce champ n’a pas d’incidence notable sur la taille de l’index.

   + **Filtrable** permet de référencer le champ dans les expressions de filtre. Chaque champ utilisé dans une expression **$filter** doit avoir cet attribut. Les expressions de filtre sont des correspondances exactes. Les chaînes de texte demeurant intactes, un stockage supplémentaire est nécessaire pour recevoir le contenu textuel.

   + **À choix multiples** active le champ pour la navigation par facettes. Seuls les champs également marqués comme **Filtrables** peuvent être marqués comme **À choix multiples**.

   + **Triable** permet d’utiliser le champ dans un tri. Chaque champ utilisé dans une expression **$Orderby** doit avoir cet attribut.

1. Avez-vous besoin d’une [analyse lexicale](search-lucene-query-architecture.md#stage-2-lexical-analysis) ? Pour les champs Edm.string de type **Searchable**, vous pouvez définir un **analyseur** si vous voulez des fonctions d’indexation et d’interrogation qui offrent une prise en charge linguistique améliorée. 

   La valeur par défaut est *Standard Lucene*, mais vous pouvez choisir *Microsoft Anglais* si vous souhaitez utiliser l’analyseur de Microsoft pour le traitement lexical avancé, tel que la résolution des formes verbales et nominales irrégulières. Seuls des analyseurs linguistiques peuvent être spécifiés sur le portail. L’utilisation d’un analyseur personnalisé ou d’un analyseur non linguistique, comme Keyword, Pattern, etc., doit s’effectuer par programmation. Pour plus d’informations sur les analyseurs, consultez [Ajouter des analyseurs linguistiques](search-language-support.md).

1. Avez-vous besoin de fonctionnalités TypeAhead matérialisées par la saisie semi-automatique ou les suggestions de résultats ? Cochez la case **Suggesteur** pour activer les [suggestions de requête TypeAhead et la saisie semi-automatique](index-add-suggesters.md) sur les champs sélectionnés. Les suggesteurs s’ajoutent au nombre de termes tokenisés de votre index et occupent donc davantage d’espace de stockage.

### <a name="indexer-configuration-in-the-wizard"></a>Configuration de l’indexeur dans l’Assistant

La dernière page de l’Assistant collecte les entrées utilisateur pour la configuration de l’indexeur. Vous pouvez [spécifier un calendrier](search-howto-schedule-indexers.md) et définir d’autres options qui varient selon le type de source de données.

En interne, l’Assistant configure également les éléments suivants, qui ne sont pas visibles dans l’indexeur tant qu’il n’a pas été créé :

+ [mappages de champs](search-indexer-field-mappings.md) entre la source de données et l’index
+ [mappages de champs de sortie](cognitive-search-output-field-mapping.md) entre la sortie de compétence et un index

## <a name="next-steps"></a>Étapes suivantes

La meilleure façon de comprendre les avantages et les limitations de l’Assistant est de le parcourir pas à pas. Le guide de démarrage rapide suivant vous guide à chaque étape.

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un index de recherche à l’aide du portail Azure](search-get-started-portal.md)