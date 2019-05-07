---
title: Mise à niveau vers le SDK .NET Azure Search version 9 - recherche Azure
description: Migrez le code pour le SDK .NET Azure Search version 9 à partir de versions antérieures. Découvrez les nouveautés et les modifications de code nécessaires.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: f540bc304920073bcd823adcf6c9dd47cb2cf93b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159749"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-9"></a>Mise à niveau vers le SDK .NET Azure Search version 9

Si vous utilisez la version 7.0-preview ou une version antérieure de la [SDK .NET Azure Search](https://aka.ms/search-sdk), cet article vous aidera à mettre à niveau votre application pour utiliser la version 9.

> [!NOTE]
> Si vous souhaitez utiliser la version 8.0-preview pour tester les fonctionnalités qui ne sont pas encore à la disposition générale, vous pouvez également suivre les instructions de cet article pour la mise à niveau vers la version préliminaire 8.0 à partir de versions antérieures.

Pour avoir un aperçu général du kit de développement logiciel et avoir des exemples, voir [Comment utiliser Azure Search à partir d’une application .NET](search-howto-dotnet-sdk.md).

Version 9 du SDK .NET Azure Search contient de nombreuses modifications par rapport aux versions antérieures. Certaines de ces modifications importantes, mais elles doivent uniquement nécessitent des modifications relativement mineures à votre code. Consultez la page [Procédure de mise à niveau](#UpgradeSteps) pour obtenir des instructions sur la façon de modifier le code à utiliser avec la nouvelle version du kit de développement logiciel.

> [!NOTE]
> Si vous utilisez la version 4.0-preview ou une version antérieure, votre doit tout d’abord mettre à niveau vers la version 5, puis mettre à niveau vers la version 9. Consultez [mise à niveau vers le SDK .NET Azure Search version 5](search-dotnet-sdk-migration-version-5.md) pour obtenir des instructions.
>
> Votre instance de service Recherche Azure prend en charge plusieurs versions de l’API REST, y compris la plus récente. Vous pouvez continuer à utiliser une version lorsqu’elle n’est pas la plus récente, mais nous vous recommandons de migrer votre code pour utiliser la dernière version. Lorsque vous utilisez l’API REST, vous devez spécifier la version de l’API dans chaque requête via le paramètre « api-version » (Version de l’API). Lorsque vous utilisez le Kit de développement logiciel (SDK) .NET, la version du Kit de développement logiciel (SDK) que vous utilisez détermine la version correspondante de l’API REST. Si vous utilisez un Kit de développement logiciel (SDK) plus ancien, vous pouvez continuer à exécuter ce code sans changement, même si le service est mis à niveau pour prendre en charge une version plus récente de l’API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Quelles sont les nouveautés de la version 9
Version 9 du SDK .NET Azure Search cible de la dernière version généralement disponible de l’API REST Azure Search, spécifiquement 2019-05-06. Cela permet d’utiliser de nouvelles fonctionnalités de Recherche Azure à partir d’une application .NET, notamment les suivantes :

* [Recherche COGNITIVE](cognitive-search-concept-intro.md) est une fonctionnalité d’intelligence artificielle dans Azure Search, utilisé pour extraire du texte à partir d’images, les objets BLOB et les autres sources de données non structurées - enrichir le contenu à rendre plus détectables dans un index Azure Search.
* Prise en charge de [des types complexes](search-howto-complex-data-types.md) vous permet de modéliser toute structure JSON imbriquée dans un index Azure Search.
* [La saisie semi-automatique](search-autocomplete-tutorial.md) fournit une alternative à la **suggérer** API pour implémenter le comportement de recherche en tant que-vous-type. La saisie semi-automatique » termine » le mot ou expression actuellement saisie d’un utilisateur.
* [Mode d’analyse de JsonLines](search-howto-index-json-blobs.md), composant d’objet Blob Azure à l’indexation, crée un document de recherche par entité JSON séparé par un saut de ligne.

### <a name="new-preview-features-in-version-80-preview"></a>Nouvelles fonctionnalités d’aperçu dans la version 8.0-version préliminaire
Version 8.0-preview du SDK .NET Azure Search cible API version 2017-11-11-Preview. Cette version inclut les mêmes fonctionnalités de version 9, ainsi que :

* [Clés de chiffrement gérées par le client](search-security-manage-encryption-keys.md) pour le chiffrement au repos côté service est une nouvelle fonctionnalité préliminaire. Outre l’intégrés chiffrement au repos géré par Microsoft, vous pouvez appliquer une couche supplémentaire d’où vous êtes l’unique propriétaire des clés de chiffrement.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Procédure de mise à niveau
Tout d’abord, mettez à jour vos références NuGet `Microsoft.Azure.Search` , soit en utilisant la console NuGet Package, soit en effectuant un clic droit sur les références de votre projet et en sélectionnant « Gérer les packages NuGet » dans Visual Studio.

Une fois que NuGet a téléchargé les nouveaux packages et leurs dépendances, recompilez votre projet. En fonction de la structure de votre code, la recompilation peut réussir. Si c’est le cas, vous êtes prêt !

Si votre build échoue, vous devez résoudre chaque erreur de build. Consultez [dernières modifications dans la version 9](#ListOfChanges) pour plus d’informations sur la façon de résoudre le potentiel de chaque erreur de build.

D’autres avertissements de compilation sont susceptibles de s’afficher ; ils sont liés à des propriétés ou des méthodes obsolètes. Ces avertissements incluent des instructions sur les fonctionnalités à utiliser à la place de la fonctionnalité déconseillée. Par exemple, si votre application utilise le `DataSourceType.DocumentDb` propriété, vous devez obtenir un avertissement indiquant « ce membre est déconseillé. Utilisez CosmosDb à la place ».

Une fois que vous avez résolu les erreurs de build ou les avertissements sur la génération, vous pouvez apporter des changements à votre application pour exploiter les nouvelles fonctionnalités, si vous le souhaitez. Nouvelles fonctionnalités dans le SDK sont détaillées dans [quelles sont les nouveautés de la version 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Modifications avec rupture dans la version 9

Il existe plusieurs modifications avec rupture dans la version 9 qui peut-être nécessiter des modifications de code en plus de la recompilation de votre application.

> [!NOTE]
> La liste des modifications ci-dessous n’est pas exhaustive. Certaines modifications ne provoquera probablement pas des erreurs de build, mais sont techniquement importantes dans la mesure où elles rompent la compatibilité binaire avec des assemblys qui dépendent de versions antérieures des assemblys de SDK .NET Azure Search. Ces modifications ne sont pas répertoriées ci-dessous. Régénérez votre application lors de la mise à niveau vers la version 9 pour éviter tout problème de compatibilité binaire.

### <a name="making-properties-immutable"></a>Rendre les propriétés immuables

Les propriétés publiques de plusieurs classes de modèle sont désormais immuables. Si vous avez besoin créer des instances de ces classes de test personnalisées, vous pouvez utiliser les nouveaux constructeurs paramétrés :

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Modifications apportées au champ

Le `Field` classe a été modifiée maintenant qu’il peut également représenter champs complexes.

Ce qui suit `bool` propriétés sont désormais nullables :

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Il s’agit, car ces propriétés doivent maintenant être `null` dans le cas des champs complexes. Si vous avez le code qui lit ces propriétés, il doit être prêt à gérer `null`. Notez que toutes les autres propriétés de `Field` ont toujours été et continuent à être nullable, et certains d'entre eux seront également `null` dans le cas des champs complexes--spécifiquement les éléments suivants :

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Le constructeur sans paramètre de `Field` a été effectuée `internal`. Dès lors, chaque `Field` requiert un type de données et le nom explicite au moment de la construction.

### <a name="simplification-of-batch-and-results-types"></a>Simplification des types de traitement par lots et résultats

Dans la version 7.0-preview et versions antérieure, les différentes classes qui encapsulent des groupes de documents ont été structurées dans des hiérarchies de classes parallèles :

  -  `DocumentSearchResult` et `DocumentSearchResult<T>` héritée `DocumentSearchResultBase`
  -  `DocumentSuggestResult` et `DocumentSuggestResult<T>` héritée `DocumentSuggestResultBase`
  -  `IndexAction` et `IndexAction<T>` héritée `IndexActionBase`
  -  `IndexBatch` et `IndexBatch<T>` héritée `IndexBatchBase`
  -  `SearchResult` et `SearchResult<T>` héritée `SearchResultBase`
  -  `SuggestResult` et `SuggestResult<T>` héritée `SuggestResultBase`

Les types dérivés sans paramètre de type générique ont été destinés à être utilisé dans les scénarios de « dynamiquement typé » et suppose l’utilisation de la `Document` type.

À compter de version 8.0-preview, les classes de base et les classes dérivées non générique ont tous été supprimés. Pour les scénarios de type dynamique, vous pouvez utiliser `IndexBatch<Document>`, `DocumentSearchResult<Document>`, et ainsi de suite.
 
### <a name="removal-of-extensibleenum"></a>Suppression de ExtensibleEnum

Le `ExtensibleEnum` classe de base a été supprimée. Toutes les classes dérivées à partir de celui-ci sont désormais structs, tel que `AnalyzerName`, `DataType`, et `DataSourceType` par exemple. Leur `Create` méthodes ont également été supprimés. Vous pouvez simplement supprimer les appels à `Create` étant donné que ces types sont convertis implicitement à partir de chaînes. Si cela entraîne des erreurs du compilateur, vous pouvez appeler explicitement l’opérateur de conversion par le biais de cast pour lever l’ambiguïté de types. Par exemple, vous pouvez modifier le code comme suit :

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

par ceci :

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Propriétés qui détenait valeurs facultatives de ces types sont maintenant explicitement de type nullable afin de continuer à être facultatif.

### <a name="removal-of-facetresults-and-hithighlights"></a>Suppression de FacetResults et HitHighlights

Le `FacetResults` et `HitHighlights` classes ont été supprimées. Résultats de la facette sont désormais typées en tant que `IDictionary<string, IList<FacetResult>>` et positionnement met en surbrillance en tant que `IDictionary<string, IList<string>>`. Un moyen rapide pour résoudre les erreurs de build introduits par cette modification consiste à ajouter `using` alias en haut de chaque fichier qui utilise les types supprimés. Par exemple : 

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Remplacez par SynonymMap 

Le constructeur `SynonymMap` n’a plus de paramètre `enum` pour `SynonymMapFormat`. Cette énumération avait une seule valeur et était donc redondante. Si vous voyez des erreurs de build suite à ce changement, supprimez simplement les références au paramètre `SynonymMapFormat`.

### <a name="miscellaneous-model-class-changes"></a>Modifications de classe de modèle divers

Le `AutocompleteMode` propriété de `AutocompleteParameters` n’est plus nullable. Si vous avez du code qui attribue à cette propriété `null`, vous pouvez simplement le supprimer et la propriété sera automatiquement initialisée à la valeur par défaut.

L’ordre des paramètres pour le `IndexAction` constructeur a changé, maintenant que ce constructeur est généré automatiquement. Au lieu d’utiliser le constructeur, nous vous recommandons d’utiliser les méthodes de fabrique `IndexAction.Upload`, `IndexAction.Merge`, et ainsi de suite.

### <a name="removed-preview-features"></a>Fonctionnalités préliminaires supprimées

Si vous mettez à niveau à partir de la version 8.0-version préliminaire vers la version 9, n’oubliez pas que le chiffrement avec des clés gérées par le client a été supprimé dans la mesure où cette fonctionnalité est toujours en version préliminaire. Plus précisément, le `EncryptionKey` propriétés de `Index` et `SynonymMap` ont été supprimés.

Si votre application a une dépendance dure sur cette fonctionnalité, vous ne serez pas en mesure de mettre à niveau vers la version 9 du SDK .NET Azure Search. Vous pouvez continuer à utiliser la version 8.0-version préliminaire. Mais n’oubliez pas que **nous déconseillons l’utilisation de Kits de développement logiciel en version préliminaire dans les applications de production**. Les fonctionnalités préliminaires servent uniquement à des fins d’évaluation et peuvent changer.

> [!NOTE]
> Si vous avez créé chiffrées index ou un synonyme de mappages à l’aide de la version 8.0-preview du Kit de développement, vous serez toujours en mesure de les utiliser et modifier leurs définitions à l’aide de la version 9 du SDK sans nuire à leur état de chiffrement. Version 9 du kit SDK n’envoie pas le `encryptionKey` propriété à l’API REST et par conséquent l’API REST ne changera pas l’état de chiffrement de la ressource. 

### <a name="behavioral-change-in-data-retrieval"></a>Changement de comportement dans la récupération des données

Si vous utilisez « dynamiquement typé » `Search`, `Suggest`, ou `Get` API qui retournent des instances de type `Document`, n’oubliez pas que leur maintenant de désérialiser des tableaux JSON vides pour `object[]` au lieu de `string[]`.

## <a name="conclusion"></a>Conclusion
Si vous souhaitez plus d’informations sur l’utilisation du Kit de développement logiciel (SDK) Azure Search .NET, consultez les articles [Procédures .NET](search-howto-dotnet-sdk.md).

N’hésitez pas à nous faire part de vos commentaires sur le kit de développement logiciel. Si vous rencontrez des problèmes, n’hésitez pas à nous demander de l’aide sur [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Si vous trouvez un bogue, vous pouvez signaler un problème dans le [Référentiel GitHub du Kit de développement logiciel (SDK) Azure .NET](https://github.com/Azure/azure-sdk-for-net/issues). N’oubliez pas de faire précéder le titre de votre problème du préfixe « [Recherche Azure] ».

Merci d’utiliser Azure Search !
