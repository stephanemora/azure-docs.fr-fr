---
title: Ajouter des analyseurs de langue à des champs de chaîne
titleSuffix: Azure Cognitive Search
description: Analyse lexicale multilingue pour les requêtes et les index formulés dans une langue autre que l’anglais dans Recherche cognitive Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 082ece269fa0e07419ff44c736fdeb19aaf30bdc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124735337"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Ajouter des analyseurs de langue à des champs de chaîne dans l’index de Recherche cognitive Azure

Un *analyseur linguistique* est un type spécifique d’[analyseur de texte](search-analyzers.md) qui effectue une analyse lexicale selon les règles linguistiques de la langue cible. Chaque champ de chaîne pouvant faire l’objet d’une recherche dispose d’une propriété **analyzer**. Si votre contenu est constitué de chaînes traduites, par exemple des champs distincts pour le texte en anglais et le texte en chinois, vous pouvez spécifier des analyseurs linguistiques sur chaque champ pour accéder aux riches fonctionnalités linguistiques de ces analyseurs.

## <a name="when-to-use-a-language-analyzer"></a>Quand utiliser un analyseur linguistique ?

Vous devez envisager le recours à un analyseur linguistique quand la reconnaissance de la structure des mots ou des phrases ajoute de la valeur à l’analyse du texte. Un exemple courant est l’association de formes de verbes irréguliers (« bring » et « brought ») ou de noms au pluriel (« mice » et « mouse »). Sans reconnaissance linguistique, ces chaînes sont analysées uniquement sur des caractéristiques physiques, et l’association n’est pas détectée. Étant donné que les grands segments de texte sont plus susceptibles de présenter ce contenu, les champs composés de descriptions, d’évaluations ou de résumés sont de bons candidats pour un analyseur linguistique.

Vous devez également prendre en compte le recours aux analyseurs linguistiques quand le contenu est constitué de chaînes en langue non occidentale. Bien que l’[analyseur par défaut (Standard Lucene)](search-analyzers.md#default-analyzer) soit indépendant de la langue, le concept d’utilisation d’espaces et de caractères spéciaux (traits d’union et barres obliques) pour séparer les chaînes s’applique davantage aux langues occidentales qu’aux autres. 

Par exemple, en chinois, japonais, coréen (CJK) et d’autres langues asiatiques, un espace n’est pas nécessairement un délimiteur de mots. Prenons la chaîne suivante en japonais. Étant donné qu’il n’y a pas d’espace, un analyseur indépendant de la langue analysera probablement la chaîne entière comme un seul jeton, alors qu’il s’agit en fait d’une phrase.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

Dans l’exemple ci-dessus, une requête correcte devrait inclure le jeton complet, ou un jeton partiel avec caractère générique de suffixe, ce qui donnerait lieu à une expérience de recherche non naturelle et restrictive.

Une meilleure expérience consiste à rechercher des mots individuels : 明るい (brillant), 私たちの (notre), 銀河系 (galaxie). L’utilisation de l’un des analyseurs japonais disponibles dans Recherche cognitive est plus susceptible de déverrouiller ce comportement, car ces analyseurs sont mieux équipés pour diviser le segment de texte en mots significatifs dans la langue cible.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Comparaison des analyseurs Lucene et Microsoft

Recherche cognitive Azure prend en charge 35 analyseurs linguistiques qui s’appuient sur Lucene et 50 analyseurs linguistiques exploitant la technologie propriétaire Microsoft de traitement du langage naturel utilisée dans Office et Bing.

Certains développeurs peuvent préférer la solution open source, plus familière et simple de Lucene. Les analyseurs linguistiques Lucene sont plus rapides, mais les analyseurs Microsoft proposent des fonctionnalités avancées, comme la lemmatisation, la décomposition en mots (dans les langues comme l’allemand, le danois, le néerlandais, le suédois, le norvégien, l’estonien, le finnois, le hongrois et le slovaque) et la reconnaissance d’entités (URL, adresses e-mail, dates, numéros). Si possible, vous devez comparer les analyseurs Microsoft et Lucene pour savoir lequel vous convient le mieux. Vous pouvez utiliser l'[API Analyser](/rest/api/searchservice/test-analyzer) pour voir les jetons générés à partir d'un texte donné à l'aide d'un analyseur spécifique.

L’indexation avec les analyseurs Microsoft est en moyenne trois fois plus lente qu’avec leurs équivalents Lucene, en fonction de la langue. Les performances de recherche ne doivent pas être trop affectées pour les requêtes de taille moyenne. 

### <a name="english-analyzers"></a>Analyseurs pour l’anglais

L’analyseur par défaut est Lucene Standard, qui fonctionne bien pour l’anglais, mais peut-être pas aussi bien que l’analyseur d’anglais de Lucene ou l’analyseur d’anglais de Microsoft.

+ L'analyseur d’anglais de Lucene est une extension de l’analyseur standard. Il supprime la marque du possessif (« ’s ») à la fin des mots, applique l’algorithme de recherche de radical de Porter et supprime les mots vides de l’anglais.  

+ L’analyseur d’anglais de Microsoft procède par lemmatisation plutôt que par recherche de radical. Il gère donc beaucoup mieux les formes fléchies et irrégulières, ce qui donne des résultats de recherche plus pertinents 

## <a name="how-to-specify-a-language-analyzer"></a>Comment spécifier un analyseur de langage

Définissez l’analyseur lors de la création de l’index avant qu’il ne soit chargé avec les données.

1. Dans la définition de champ, vérifiez que le champ est attribué avec la valeur « pouvant faire l’objet d’une recherche » et qu’il est de type Edm.String.

1. Définissez la propriété « analyzer » sur l’un des analyseurs de langue de la [liste des analyseurs pris en charge](#language-analyzer-list).

   La propriété « analyzer » est la seule propriété qui accepte un analyseur de langue, et elle est utilisée à la fois pour l’indexation et les requêtes. Les autres propriétés associées à l’analyseur (« searchAnalyzer » et « indexAnalyzer ») n’acceptent pas un analyseur de langue.

Les analyseurs de langue ne peuvent pas être personnalisés. Si un analyseur ne répond pas à vos besoins, vous pouvez essayer de créer un [analyseur personnalisé](cognitive-search-working-with-skillsets.md) avec microsoft_language_tokenizer ou microsoft_language_stemming_tokenizer, et d’ajouter des filtres pour le traitement avant et après segmentation du texte en unités lexicales.

L’exemple suivant illustre une spécification de l’analyseur de langue dans un index :

```json
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
```

Pour plus d’informations sur la création d’un index et la définition de propriétés de champ, consultez [Créer un index (REST)](/rest/api/searchservice/create-index). Pour plus d’informations sur l’analyse de texte, consultez [Analyseurs dans Recherche cognitive](search-analyzers.md).

<a name="language-analyzer-list"></a>

## <a name="supported-language-analyzers"></a>Analyseurs de langage pris en charge

 Voici la liste des langages pris en charge avec les noms d’analyseur Lucene et Microsoft.  

| Langage | Nom de l’analyseur Microsoft | Nom de l’analyseur Lucene |
|----------|-------------------------|----------------------|
| Arabe   | ar.microsoft | ar.lucene |
| Arménien |           | hy.lucene |
| Bangla   | bn.microsoft |  |
| Basque   |  | eu.lucene |
| Bulgare | bg.microsoft | bg.lucene |
| Catalan  | ca.microsoft | ca.lucene |
| Chinois (simplifié) | zh-Hans.microsoft | zh-Hans.lucene |
| Chinois traditionnel | zh-Hant.microsoft | zh-Hant.lucene |
| Croate | hr.microsoft |  |
| Tchèque | cs.microsoft | cs.lucene |
| Danois | da.microsoft | da.lucene |
| Néerlandais | nl.microsoft | nl.lucene |
| Anglais | en.microsoft | en.lucene |
| Estonien | et.Microsoft |  |
| Finnois | fi.microsoft | fi.lucene |
| Français | fr.Microsoft | fr.lucene |
| Galicien |  | gl.lucene |
| Allemand | de.Microsoft | de.lucene |
| Grec | el.microsoft | el.lucene |
| Goudjrati | gu.microsoft |  |
| Hébreu | he.microsoft |  |
| Hindi | hi.microsoft | hi.lucene |
| Hongrois | hu.microsoft | hu.lucene |
| Islandais | is.microsoft |  |
| Indonésien | id.microsoft | id.lucene |
| Irlandais |  | ga.lucene |
| Italien | it.microsoft | it.lucene |
| Japonais | ja.Microsoft | ja.lucene |
| Kannada | kn.microsoft |  |
| Coréen | ko.microsoft | ko.lucene |
| Letton | lv.microsoft | lv.lucene |
| Lituanien | lt.microsoft |  |
| Malayalam | ml.microsoft |  |
| Malais (latin) | ms.microsoft |  |
| Marathi | mr.microsoft |  |
| Norvégien | nb.Microsoft | no.lucene |
| Persan |  | fa.lucene |
| Polonais | pl.microsoft | pl.lucene |
| Portugais (Brésil) | pt-Br.microsoft | pt-Br.lucene |
| Portugais (Portugal) | pt-Pt.microsoft | pt-Pt.lucene |
| Pendjabi | pa.microsoft |  |
| Roumain | ro.microsoft | ro.lucene |
| Russe | ru.microsoft | ru.lucene |
| Serbe (cyrillique) | sr-cyrillic.microsoft |  |
| Serbe (latin) | sr-latin.microsoft |  |
| Slovaque | sk.microsoft |  |
| Slovène | sl.microsoft |  |
| Espagnol | es.Microsoft | es.lucene |
| Suédois | sv.microsoft | sv.lucene |
| Tamoul | ta.microsoft |  |
| Télougou | te.microsoft |  |
| Thaï | th.microsoft | th.lucene |
| Turc | tr.microsoft | tr.lucene |
| Ukrainien | uk.microsoft |  |
| Ourdou | ur.microsoft |  |
| Vietnamien | vi.microsoft |  |

 Tous les analyseurs dont le nom est annoté **lucene** s’appuient sur les [analyseurs linguistiques d’Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Voir aussi  

+ [Création d'un index](search-what-is-an-index.md)
+ [Créer un index multilingue](search-language-support.md)
+ [Créer un index (API REST)](/rest/api/searchservice/create-index)  
+ [Classe LexicalAnalyzerName (Kit de développement logiciel (SDK) Azure pour .NET)](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)
