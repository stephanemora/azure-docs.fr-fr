---
title: Créer un index de Recherche Azure dans le portail | Microsoft Docs
description: Découvrez comment créer un index pour Recherche Azure à l’aide des concepteurs d’index intégrés du portail.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: bb1ba5e860dab237b3f6e16205b5e4cbad45e6e3
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990844"
---
# <a name="how-to-create-an-azure-search-index-using-the-azure-portal"></a>Comment créer un index de Recherche Azure à l’aide du portail Azure

Recherche Azure inclut un concepteur d’index intégré dans le portail, très utile pour des prototypes ou pour créer un [index de recherche](search-what-is-an-index.md) hébergé sur votre service Recherche Azure. L’outil est utilisé pour la construction de schéma. Lorsque vous enregistrez la définition, un index vide devient entièrement exprimé dans Recherche Azure. Le choix de la manière de le charger à l’aide de données interrogeables vous appartient.

Le concepteur d’index est une approche parmi d’autres pour la création d’un index. Vous pouvez également créer un index par programmation à l’aide des API [.NET](search-create-index-dotnet.md) ou [REST](search-create-index-rest-api.md).

## <a name="prerequisites"></a>Prérequis

Cet article repose sur le principe que vous disposez [d’un abonnement Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) et du [service Recherche Azure](search-create-service-portal.md).

## <a name="open-index-designer-and-name-an-index"></a>Ouvrir le concepteur d’index et nommer un index

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez le tableau de bord du service. Vous pouvez cliquer sur **Tous les services** dans la barre d’index pour afficher les « services de recherche » existants dans l’abonnement actuel. 

2.  Cliquez sur le bouton **Ajouter un index** de la barre de commandes en haut de la page.

3. Nommez votre index Azure Search. Les noms d’index sont référencés dans les opérations d’indexation et de requête. Le nom de l’index est ensuite intégré dans l’URL de point de terminaison utilisé pour les connexions à l’index, et il est utilisé pour l’envoi de requêtes HTTP dans l’API REST Azure Search.

   * Le nom doit commencer par une lettre.
   * N’utilisez que des lettres minuscules, chiffres ou tirets (« - »).
   * Le nom ne doit pas dépasser 60 caractères.

## <a name="define-the-fields-of-your-index"></a>Définir les champs de l’index

La composition de l’index inclut une *collection de champs* qui définit les données qu’il est possible de rechercher dans votre index. Globalement, la collection de champs spécifie la structure des documents que vous chargez séparément. Une collection de champs inclut des champs obligatoires et facultatifs, nommés et typés, avec des attributs d’index déterminant la façon dont le champ peut être utilisé.

1. Dans le panneau **Ajouter un index**, cliquez sur **Champs >** pour ouvrir le panneau de définition de champ. 

2. Acceptez le champ *clé* de type Edm.String généré. Par défaut, le champ est nommé *id*, mais vous pouvez le renommer tant que la chaîne est conforme aux [règles d’affectation de noms](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Chaque index Azure Search doit obligatoirement comporter un champ clé de type chaîne.

3. Ajoutez des champs pour fournir des spécifications complètes sur les documents à charger. Si les documents comportent un *id*, un *nom d’hôtel*, une *adresse*, une *ville* et une *région*, créez un champ correspondant pour chacun de ces éléments dans l’index. Passez en revue les [conseils de conception de la section ci-dessous](#design) pour vous aider à définir les attributs.

4. Si vous le souhaitez, ajoutez tous les champs qui sont utilisés en interne dans les expressions de filtre. Les attributs des champs peuvent être définis pour les exclure des opérations de recherche.

5. Lorsque vous avez terminé, cliquez sur **OK** pour enregistrer et créer l’index.

## <a name="tips-for-adding-fields"></a>Conseils sur l’ajout de champs

La création d’un index dans le portail demande énormément de manipulations clavier. Allégez les différentes étapes en suivant ce processus :

1. Commencez par créer la liste des champs en saisissant leurs noms et en définissant les types de données associés.

2. Utilisez ensuite les cases à cocher au-dessus de chaque attribut pour activer un paramètre sur tous les champs simultanément, puis décochez les quelques cases superflues le cas échéant. Par exemple, il est généralement possible d’effectuer des recherches sur les champs de type chaîne. Par conséquent, vous pouvez cliquer sur **Récupérable**  et **Possibilité de recherche** pour permettre à la fois le renvoi des valeurs du champ dans les résultats de recherche, ainsi qu’une recherche en texte intégral sur ce champ. 

<a name="design"></a>

## <a name="design-guidance-for-setting-attributes"></a>Conseils de conception pour la définition des attributs

Même si vous pouvez ajouter de nouveaux champs à tout moment, les définitions de champ existantes sont verrouillées pour toute la durée de vie de l’index. C’est pourquoi les développeurs utilisent généralement le portail pour créer des index simples, tester des idées ou rechercher une définition de paramètre. Il est plus efficace d’effectuer des itérations fréquentes sur la conception d’un index si vous suivez une approche basée sur du code pour reconstruire l’index facilement.

Des analyseurs et générateurs de suggestions sont associés aux champs avant l’enregistrement de l’index. Veillez à cliquer sur chaque onglet pour ajouter des analyseurs de langage ou des générateurs de suggestions à la définition de votre index.

Les champs de type chaîne sont souvent marqués avec les attributs **Possibilité de recherche** et **Récupérable**.

Vous pouvez utiliser les champs **Triable**, **Filtrable** et **À choix multiples** pour affiner les résultats de recherche.

Les attributs d’un champ déterminent son utilisation, par exemple s’il est utilisé dans la recherche en texte intégral, la navigation par facettes, les opérations de tri et ainsi de suite. Le tableau suivant décrit chaque attribut.

|Attribut|Description|  
|---------------|-----------------|  
|**searchable**|Recherche en texte intégral, avec analyse lexicale (césure de mots) lors de l’indexation. Si vous définissez un champ avec possibilité de recherche sur une valeur comme « journée ensoleillée », cette valeur est fractionnée au niveau interne en jetons individuels « journée » et « ensoleillée ». Pour en savoir plus, consultez la rubrique [Fonctionnement de la recherche en texte intégral](search-lucene-query-architecture.md).|  
|**filterable**|Référencé dans les requêtes **$filter**. Les champs filtrables de type `Edm.String` ou `Collection(Edm.String)` ne font pas l’objet d’une analyse lexicale, les comparaisons ne concernent donc que les correspondances exactes. Par exemple, si vous définissez un champ avec la valeur « journée ensoleillée », la requête `$filter=f eq 'sunny'` ne renverra aucune correspondance, contrairement à `$filter=f eq 'sunny day'`. |  
|**sortable**|Le système trie les résultats par score par défaut, mais vous pouvez configurer le tri en fonction des champs des documents. Les champs de type `Collection(Edm.String)` ne sont pas **triables**. |  
|**facetable**|Généralement utilisé dans une présentation des résultats de recherche qui inclut un nombre de correspondances par catégorie (par exemple, les hôtels dans une ville spécifique). Cette option ne peut pas être utilisée avec des champs de type `Edm.GeographyPoint`. Les champs de type `Edm.String` qui sont **filtrables**, **triables** ou **à choix multiples** ne peuvent pas dépasser 32 Ko de longueur. Pour plus d’informations, consultez l’article [Créer un index (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**key**|Identificateur unique des documents dans l’index. Un seul champ doit être choisi comme champ clé et il doit être de type `Edm.String`.|  
|**retrievable**|Définit si le champ peut être retourné dans un résultat de recherche. Cet attribut est utile quand vous voulez utiliser un champ (comme *profit margin*) comme mécanisme de filtre, de tri ou de score, mais que vous ne voulez pas qu’il soit visible par l’utilisateur final. Il doit être `true` for `key` .|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>Créer l’index des hôtels utilisé dans les exemples de sections d’API

La documentation de l’API Azure Search inclut des exemples de code utilisant un index *hotels* simple. Dans les captures d’écran ci-dessous, vous pouvez voir la définition de l’index, qui inclut l’analyseur de langue française spécifié lors de la définition de l’index, que vous pouvez recréer comme vous entraîner dans le portail.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé un index Azure Search, vous pouvez passer à l’étape suivante : [charger des données interrogeables dans l’index](search-what-is-data-import.md).

Sinon, vous pouvez étudier les index de manière plus approfondie. Outre la collection de champs, un index spécifie également des analyseurs, des générateurs de suggestions, des profils de score et des paramètres CORS. Le portail comprend des onglets permettant de définir les éléments les plus courants : les champs, les analyseurs et les générateurs de suggestions. Pour créer ou modifier d’autres éléments, vous pouvez utiliser l’API REST ou le kit de développement logiciel (SDK) .NET.

## <a name="see-also"></a>Voir aussi

 [Fonctionnement de la recherche en texte intégral](search-lucene-query-architecture.md)  
 [API REST du service de recherche](https://docs.microsoft.com/rest/api/searchservice/)[SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

